# Huawei-TechArena-SubTopic-2

## Algorithm

\begin{algorithm}[H]
\caption{Fast Beam switching Algorithm for sparse weight generation}
\label{alg:beam_handover}
\begin{algorithmic}[1]
\Require
Number of elements $N \times N$, spacing $d = 0.5\lambda$, 
beam specifications $\{ \theta_i, \phi_i, BW^{az}_i, BW^{el}_i \}$,
array height $h$, deactivation ratio $\rho$, 
handover hysteresis $\Delta G$, time-to-trigger $T_{TTT}$
\Ensure
Optimized beam weights $\mathbf{W}_A$, thinned weights $\mathbf{W}_B$,
and real-time active beam selection

\State Initialize steering phase matrix $\Phi(\theta,\phi)$ using element indices $(m,n)$
\State Initialize amplitude taper $A(m,n)$ using 2D Hann or Gaussian window

\For{each beam specification $(\theta_i, \phi_i, BW^{az}_i, BW^{el}_i)$}
    \State Design beam weights (Task A): 
          $\mathbf{W}_A(i) = A(m,n) \odot e^{-j k d (m u_i + n v_i)}$
    \State Measure main-lobe gain, HPBW, and SLL for $\mathbf{W}_A(i)$
    \State Generate thinning mask $M_\rho(m,n)$ (e.g., blue-noise strategy)
    \State Apply thinning and retapering (Task B):
          $\mathbf{W}_B(i) = \text{Normalize}\!\left( M_\rho \odot A(m,n) \odot e^{-j k d (m u_i + n v_i)} \right)$
    \State Evaluate directional gain $\Delta G_i = G_B - G_A$
\EndFor

\Statex
\Comment{--- Real-time beam handover (seamless switching) ---}
\State Initialize current beam $b_{cur} = \arg\max_i G_A(\theta_i, \phi_i)$
\For{each time step $t$ (every $\Delta t$ ms)}
    \State Get user position $(x_t, y_t)$ and satellite state $(x_s, y_s, h)$
    \State Compute target beam angles:
          $\theta_t = \tan^{-1}\!\left( \frac{\sqrt{x_t^2+y_t^2}}{h} \right)$, 
          $\phi_t = \tan^{-1}\!\left( \frac{y_t}{x_t} \right)$
    \State Find nearest candidate beam $b_{cand} = \arg\min_i[(\theta_i-\theta_t)^2+(\phi_i-\phi_t)^2]$
    \If{$G(b_{cand}) - G(b_{cur}) > \Delta G$ for $T_{TTT}$ ms}
        \State Switch active beam: $b_{cur} \gets b_{cand}$
    \EndIf
\EndFor

\end{algorithmic}
\end{algorithm}
