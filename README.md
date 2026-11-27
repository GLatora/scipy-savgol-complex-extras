# Complex signals in `scipy.signal.savgol_filter`: supporting material

This repository contains the supporting material for my project for the course
20875 Software Engineering. The project is a contribution
to [SciPy](https://github.com/scipy/scipy): adding support for complex signals
to the Savitzky–Golay filter, `scipy.signal.savgol_filter`, related to
[issue #19771](https://github.com/scipy/scipy/issues/19771).

**The changes to SciPy's code are in the pull request
[scipy/scipy#26784](https://github.com/scipy/scipy/pull/26784)**, not in this
repository. Here you will find the report on how I worked on it, the outputs
of the tests I ran, and two notebooks: one showing that the released
`savgol_filter` discards the imaginary part of complex input, and one applying
the fixed filter to a quantum wave function.

## The issue and the fix, in short

The Savitzky–Golay filter is a weighted sum of the samples with real weights,
so it applies to complex signals just as to real ones. The released
`savgol_filter`, however, converts its input to real numbers before filtering,
discarding the imaginary part. The pull request keeps complex input as it is,
and adds tests checking that filtering a complex signal gives the same result
as filtering its real and imaginary parts separately.

## Contents

| Item | What it is |
| --- | --- |
| `report/` | The report on the project |
| `test_outputs/` | Outputs of the test runs for the different versions of the code |
| `Savitzky-Golay_filter.ipynb` | Notebook 1: how the released SciPy handles complex input |
| `Complex_Savitzky-Golay_Application.ipynb` | Notebook 2: the fixed filter applied to a quantum wave function |

### Report

A short report describing my process: how SciPy's development workflow works
and how I set it up, how I approached the problem by writing the tests before
changing the code, why the first fix that seemed obvious was not the right one,
and the unexpected bug that only appeared when running the tests with a
different array backend.

### Test outputs

The outputs of the new and existing `savgol_filter` tests, run locally on
different versions of the code:

- **without the fix**: the released code, where the new complex tests fail;
- **with a first, simpler fix**: a version that handled complex input but
  broke other cases, which is why I did not keep it;
- **with the final fix**: all tests pass;

### Notebook 1: `Savitzky-Golay_filter.ipynb`

Runs on the **released** version of SciPy. It first
applies the filter to noisy sine wave samples, for smoothing and for computing their first
derivative, and compares the obtained derivatives with those computed directly from
the noisy samples by finite differences. It then applies the same filter to a
complex signal, consisting of noisy points on the unit circle, and shows that the imaginary
part is discarded, both when smoothing and when estimating derivatives.

### Notebook 2: `Complex_Savitzky-Golay_Application.ipynb`

Runs on the **fixed** version of SciPy from the pull request and applies the
filter to a physical example where complex numbers are essential: a quantum
wave function. Following the idea of Lundeen et al. (2011), who measured the
real and imaginary parts of a photon's wave function point by point, it
simulates noisy samples of a known wave function on an evenly spaced grid. It
then smooths them, estimates the first and second derivatives with the filter,
and uses them to estimate the expected momentum and kinetic energy, which are
compared with the values computed by hand.

## Running the notebooks

Notebook 1 only needs NumPy, SciPy and Matplotlib, with any released SciPy
version.

Notebook 2 needs SciPy built from the branch of the pull request. Following
the [SciPy contributor guide](https://scipy.github.io/devdocs/building/index.html):

```bash
git clone https://github.com/GLatora/scipy.git
cd scipy
git checkout savgol-complex
git submodule update --init
spin build
```

The first cell of the notebook adds the build to Python's path with
`sys.path.insert`. The path there is the one on my computer, so change it to
your own `build-install/usr/lib/python3.X/site-packages` folder. The cell then
prints the SciPy version, which should end in `dev0`, and checks that complex
input is kept.

## References

- SciPy issue [#19771](https://github.com/scipy/scipy/issues/19771) and pull
  request [#26784](https://github.com/scipy/scipy/pull/26784).
- A. Savitzky and M. J. E. Golay, "Smoothing and Differentiation of Data by
  Simplified Least Squares Procedures", *Analytical Chemistry* 36 (1964).
- R. W. Schafer, "What Is a Savitzky-Golay Filter? [Lecture Notes]", *IEEE
  Signal Processing Magazine* 28(4) (2011),
  [doi:10.1109/MSP.2011.941097](https://doi.org/10.1109/MSP.2011.941097).
- S. J. Orfanidis, *Introduction to Signal Processing*, Prentice Hall (1996;
  online edition 2010), Section 8.3.5 "Savitzky-Golay Smoothing Filters".
- J. S. Lundeen, B. Sutherland, A. Patel, C. Stewart and C. Bamber, "Direct
  measurement of the quantum wavefunction", *Nature* 474 (2011);
  [arXiv:1112.3575](https://arxiv.org/abs/1112.3575).
- D. J. Griffiths, *Introduction to Quantum Mechanics*, Prentice Hall (1995),
  Section 1.5 "Momentum", for the expectation values of momentum and kinetic
  energy used in Notebook 2.
  
