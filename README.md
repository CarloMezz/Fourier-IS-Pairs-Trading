# Fourier-IS-Pairs-Trading
The code executes a pairs trading strategy based on two correlated stocks, where a long-and-short position is open whenever the log-difference spread between the two stocks gets too large (in absolute value).  
By inserting the tickers of the firms you want to test the strategy on, the code downloads from Yahoo Finance one-minute data for the previous *four* days, eliminates the minutes where for some reason the data of one of the stock is not available and then operates the strategy on each day, keeping track of the number of operations and their returns after fees.

# Trading Strategy
The trading strategy requires daily data of two correlated stock $`\{X_t\}_{t=1}^n,\{X_t\}_{t=1}^n`$ and supposes that both stocks are driven by a stochastic volatility model of the following form:
```math
    \begin{cases}
        dX_t=\mu_XX_tdt+\sigma_t^XX_tdB_t^X\\
        \sigma_t^X=\exp(\frac{M_t}{2})\\
        dM_t=\kappa_X(m_X-M_t)dt+\xi_XdW_t^X
    \end{cases}\ \ \ \begin{cases}
        dY_t=\mu_YY_tdt+\sigma_t^YY_tdB_t^Y\\
        \sigma_t^Y=\exp(\frac{N_t}{2})\\
        dN_t=\kappa_Y(m_Y-N_t)dt+\xi_YdW_t^Y
    \end{cases}
```
where the correlation is reflected in  $`dB_t^XdB_t^Y=\rho_tdt`$ and, after some computations, the model of the log-difference spread is:
```math
dZ_t=\big(\mu_X-\mu_Y-\frac{1}{2}(\sigma_t^X)^2+\frac{1}{2}(\sigma_t^Y)^2\big)dt +\sqrt{(\sigma_t^X)^2+(\sigma_t^Y)^2-2\rho_t\sigma_t^X\sigma_t^Y}dB_t
```
1) Compute the log-difference spread of the data, namely $`Z_t:=\log(X_t)-\log(Y_t)=\log(\frac{X_t}{Y_t})`$.
2) Define a training window of length $K$ and compute the standardized rolling series $`z_t:=\frac{Z_t-\mu^Z_t}{\sigma^Z_t}`$, where $\mu_t^Z$ is the $K$-moving average and $\sigma_t^Z$ the $K$-moving standard deviation.
3) When the signal gets too large in absolute value $|z_t|>\phi>0$, use the Fourier function to estimate the parameters of the underlying model using the last $K$ data to get the MLE-estimated parameters $`\hat{\kappa}_X,\hat{m}_X,\hat{\xi}_X,\hat{\mu}_X`$ for $X$, $\hat{\kappa}_Y,\hat{m}_Y,\hat{\xi}_Y,\hat{\mu}_Y$ for $Y$ and $\hat{\rho}$.
