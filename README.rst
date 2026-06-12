====================
CAMB-chiral-tensors
====================

This repository is a modified version of `CAMB <https://camb.info/>`_, based on the official CAMB code and intended
for public use of primordial chiral tensor calculations that generate tensor-driven EB/TB in the CMB.

Upstream CAMB
=============

- Original code: `cmbant/CAMB <https://github.com/cmbant/CAMB>`_
- Original authors: Antony Lewis and Anthony Challinor
- CAMB homepage: https://camb.info/

This repository keeps CAMB's original license text in ``LICENCE.txt``. The original CAMB license and any upstream
conditions continue to apply to this modified distribution.

What Is Added Here
==================

This fork adds a minimal public implementation of primordial parity-violating tensor spectra:

- ``tensor_PV`` to enable the chiral tensor calculation path
- primordial parameters ``r_vac``, ``r_ast``, ``sig``, ``kp``, ``calP_zeta``
- tensor EB/TB support through the Python results interface

The chiral gravitational-wave contribution to the CMB EB/TB spectra implemented here follows:

- B. Thorne, T. Fujita, M. Hazumi, N. Katayama, E. Komatsu and M. Shiraishi,
  ``Finding the chiral gravitational wave background of an axion-SU(2) inflationary model using CMB observations
  and laser interferometers'', Phys. Rev. D 97 (2018) no.4, 043506,
  `doi:10.1103/PhysRevD.97.043506 <https://doi.org/10.1103/PhysRevD.97.043506>`_,
  `arXiv:1707.03240 <https://arxiv.org/abs/1707.03240>`_.

The original EB/TB chiral-GW implementation used for this repository was developed with implementation guidance
from M. Shiraishi.

For the Python API, tensor-related spectra are returned in the order::

    TT, EE, BB, TE, EB, TB

In particular, ``get_tensor_cls()``, ``get_total_cls()`` and ``get_cmb_power_spectra()["tensor"/"total"]``
return six columns in that order.

Build
=====

Clone with submodules::

    git clone --recursive https://github.com/YutoMinami/CAMB-chiral-tensors.git

To build the shared library from the Fortran side::

    cd CAMB-chiral-tensors/fortran
    make python PYCAMB_OUTPUT_DIR=../camb/

To build the command-line executable::

    cd CAMB-chiral-tensors/fortran
    make camb

Usage
=====

Example command-line run::

    ./fortran/camb inifiles/planck_2018_GWEB.ini

Minimal Python example::

    import camb
    pars = camb.read_ini("inifiles/planck_2018_GWEB.ini")
    results = camb.get_results(pars)
    tensor_cls = results.get_tensor_cls()
    total_cls = results.get_total_cls()
    # Column order: TT, EE, BB, TE, EB, TB
    eb = tensor_cls[:, 4]
    tb = tensor_cls[:, 5]

Notes
=====

- ``inifiles/planck_2018_GWEB.ini`` provides a minimal example configuration for primordial EB/TB.
- A compatibility check against legacy spectra used in ``ForPrimordialEB/LimitToChiralGW.ipynb`` was carried out
  with the old file ``planck_2018_sig2.000e-01_kp5.000e-02.pkl``. The tensor ``BB``, ``EB`` and ``TB`` spectra
  were reproduced by this fork to better than about ``1e-4`` in relative peak amplitude.
- The EB/TB chiral-GW part is an extension on top of CAMB and should be cited separately from upstream CAMB when
  used in scientific work.
- This repository is focused on the CAMB modification itself; analysis notebooks and plotting pipelines are not
  included here.
