# Hubble Tension

Measuring the Hubble constant $H_0$ three independent ways (Cepheid distance
ladder, Type Ia supernovae, CMB power spectrum) and comparing the results.

The [Hubble tension](https://en.wikipedia.org/wiki/Hubble%27s_law#Hubble_tension)
is the disagreement between $H_0$ measured locally and $H_0$ inferred from the
CMB assuming $\Lambda$CDM. Everything here is derived from raw data rather than
quoted.

## Results

Start with **[`Hubble_tension.ipynb`](Hubble_tension.ipynb)**, which combines all
three methods. The rest of the repo derives its three numbers.

![H0 comparison](Saved%20Data/H0_comparison.png)

| Method | Model | $H_0$ (km/s/Mpc) | Other parameters |
|---|---|---|---|
| Cepheids (single cluster) | | $53.5 \pm 43.0$ | $d = 16.3 \pm 3.6$ Mpc |
| SNe Ia (Pantheon) | $\Lambda$CDM | $70.18 \pm 0.22$ | $\Omega_m = 0.284 \pm 0.012$ |
| SNe Ia (Pantheon) | wCDM | $70.45 \pm 0.28$ | $\Omega_m = 0.323 \pm 0.022$, $w = -1.134 \pm 0.074$ |
| CMB (Planck TT) | $\Lambda$CDM | $64.96 \pm 1.86$ | $\Omega_b h^2 = 0.02117$, $\Omega_c h^2 = 0.1218$, $n_s = 0.949$ |
| CMB (Planck TT) | wCDM | $71.31 \pm 10.82$ | $w = -1.207 \pm 0.380$ |

Late-time and early-time disagree:

$$\Delta H_0 = 70.18 - 64.96 = 5.22 \ \text{km/s/Mpc}, \qquad
n_\sigma = \frac{\Delta H_0}{\sqrt{\sigma_1^2 + \sigma_2^2}} = 2.8$$

The published gap (SH0ES $73.04$ vs Planck $67.36$) is $5.68$ at $4.8\sigma$. Same
sign, same size, lower significance.

Caveats: quoted errors are chain standard deviations, so they ignore
autocorrelation and run optimistic. The CMB $H_0$ is low because the spectrum uses
an approximate $1/f_{\rm sky}$ mask correction instead of a full MASTER
deconvolution. This reproduces the structure of the tension, not its significance.

## Layout

### [`Hubble_tension.ipynb`](Hubble_tension.ipynb)

Recomputes all three $H_0$ values from the saved chains and data, plots them
against SH0ES and Planck, and quantifies the tension.

### [`Cepheid Variables/`](Cepheid%20Variables)

The distance ladder. Calibrate the Leavitt law $M = \alpha \log_{10} P + \beta$ on
Gaia Cepheids, apply it to Cepheids in a galaxy cluster, then fit $v = H_0 d$.

- `Cepheids.ipynb` (Sections 1 to 4)
- `Data/galaxy.csv`, `Data/gaia.csv`, `Data/cepheid.csv`

### [`SNe/`](SNe)

Pantheon Type Ia supernovae fitted with Metropolis-Hastings MCMC written from
scratch, using $\mu = 5\log_{10}(D_L/\text{Mpc}) + 25$.

- `Cosmological_distances.ipynb`: $D_C$, $D_A$, $D_L$ and the distance modulus
- `SNe_mcmc.ipynb`: standard MH-MCMC for ($H_0$, $\Omega_m$), then $+\Omega_r$
- `SNe_optimized_mcmc.ipynb`: same sampler, $O(N)$ chain storage
- `SNe_adap_mcmc.ipynb`: adaptive MH-MCMC for wCDM, plus the SNe/CMB comparison
- `Pantheon Data/`, `Saved Data/`

### [`CMB/`](CMB)

Extract $D_\ell = \ell(\ell+1)C_\ell/2\pi$ from the Planck `commander` map and fit
it against CAMB.

- `CMB_Planck.ipynb`: mask the map, estimate $C_\ell$ with `healpy.anafast`
- `CMB_ell_vs_Cl_Dl.ipynb`: why $D_\ell$ rather than $C_\ell$
- `CMB_camb.ipynb`: how the peaks move with each parameter
- `CMB_MCMC.ipynb`, `CMB_MCMC_adaptive.ipynb`: fit $H_0$, $\Omega_b h^2$, $\Omega_c h^2$, $A_s$, $n_s$
- `CMB_MCMC_wCDM.ipynb`: same with $w$ free
- `CMB_pie_chart.ipynb`: energy budget
- `Plancks Data/`, `Saved Data/`

**Note:** the Planck map (`COM_CMB_IQU-commander_2048_R3.00_full.fits`, 1.6 GB) is
not tracked. Download from the [Planck Legacy Archive](https://pla.esac.esa.int/)
into `CMB/Plancks Data/`. Derived products (`APS_anafast.txt`,
`COM_PowerSpect_CMB-*.txt`) are included.

## Running locally

```bash
pip install -r requirements.txt
jupyter notebook
```

Run each folder in the order listed above, since later notebooks depend on earlier
outputs. The MCMC notebooks load saved chains from `Saved Data/`, so plots can be
regenerated without re-running the samplers.

Every notebook ends with a conclusion, so each can be read on its own.

## License

[MIT](LICENSE)
