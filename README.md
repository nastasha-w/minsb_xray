+--------------+
| Introduction |
+--------------+

I used this code (before a few slight modifications to make it a 
stand-alone set of scripts) in Wijers & Schaye (2022; ADS link: 
https://ui.adsabs.harvard.edu/abs/2022MNRAS.514.5214W/abstract).
Please cite this paper if you use this code. (bibtex entry below)
Please remember to also cite the sources for the backgrounds and 
response files you use.

It uses the instrument responses and instrumental + astrophysical
background to calculate the minimum emission line surface brightness 
a source needs to reach a given signal-to-noise ratio in a given 
exposure time and spatial binning.

+-----+
| Use |
+-----+

'Installation' and warnings
---------------------------

I have not put this together as a full installable package; this is 
just a collection of scripts best run from the directory they are in.
However, as installing the required sherpa package can be tricky, I 
have included a conda environment file that can be used to set up the
required python packages in a virtual environment.

This code also required models for the instrument responses and 
instrumental and astrophysical backgrounds. I have not included those
there because the files would easily become outdated without 
maintenance, and the same configuration and response fiel might not
be appropriate for every situation. (E.g., on-axis vs. off-axis,
focussed vs. unfocussed, different options for in-development 
instruments.)

I have included a fiducial model for Galactic absorption (because I
wanted minimum intrinsic surface brightnesses for extragalactic 
sources). This is a simple `wabs` model in Xspec, and you might need
to use a different model (or a model with a different hydrogen column
density) for your own applications.

For the paths and instrument responses, you will need to modify the 
code under `##paths` near the top of `calc_minsb.py`. 
`mdir` is the directory where plotted images are stored
`dir_insfiles` is the directory where response files and backgrounds
are stored. You will also need to modify the paths for the response
files and backgrounds for different instruments.

The `Responses` class handles the .arf and .rmf files; that should be
fairly straightforward. However, you will likely need to modify or add
instrument options for the `InstrumentModel` class. This is because 
different instruments come with different background file formats,
e.g., saved in a single file or split into multiple components. Also 
note that data on e.g., the opening angle over which the backgrounds
are given also differs between files. This needs to be added by hand.


Main functions
--------------

The `InstrumentModel` class is the workhorse of the code, used to 
calculate actual minimum surface brightness levels using its 
`getminSB_grid` function. (The 'grid' refers to the fact that it can
take an array of line energies as input.)

`getminSB_grid` requires 
- the line energies (rest-frame) and redshift,
- line width, 
- signal-to-noise level (number of sigmas) required for detection,
- exposure time x spatial binning area, 
- spectral range over which to measure the signal and the noise, and
- whether to include Galactic absorption in the minimum surface 
  brightness
to calculate the minimum surface brightness values.

Depending on the spectral resolution of the instrument and expected
line widths, it might be useful to experiment with different line width
values.

The spectral range over which signal and noise are measured can be be
explored using the `explorepars_omegat_extr` function. Just modify the
`extr_ranges` dictionary to try out different values (integer 
extraction ranges specify a number of channels), and possibly different
instruments. Note that `delta_E_chan` (the channel widths in eV) is 
simply used to report the size of the extraction range in eV.

I used `savetable_sbmin` to calculate the set of minimum surface 
brightnesses used in Wijers & Schaye (2022). This can be modified to
calculate a different set of minima.


+-------+
| Notes |
+-------+

papers to cite for this code:
-----------------------------

Please remember to also cite the sources for the backgrounds and 
response files you use. Some sources are only needed if you use the
included line wavelength lists, Galactic absorption model, or plots.

Wijers & Schaye (2022): paper describing the calculations and applying
calculated minimum observable surface brightnesses to investigate 
detectability of emission lines from galaxy haloes.
```
@ARTICLE{2022MNRAS.514.5214W,
       author = {{Wijers}, Nastasha A. and {Schaye}, Joop},
        title = "{The warm-hot circumgalactic medium around EAGLE-simulation galaxies and its detection prospects with X-ray-line emission}",
      journal = {\mnras},
     keywords = {galaxies: formation, galaxies: groups: general, galaxies: haloes, large-scale structure of Universe, X-rays: galaxies, Astrophysics - Astrophysics of Galaxies, Astrophysics - Cosmology and Nongalactic Astrophysics},
         year = 2022,
        month = aug,
       volume = {514},
       number = {4},
        pages = {5214-5237},
          doi = {10.1093/mnras/stac1580},
archivePrefix = {arXiv},
       eprint = {2108.04847},
 primaryClass = {astro-ph.GA},
       adsurl = {https://ui.adsabs.harvard.edu/abs/2022MNRAS.514.5214W},
      adsnote = {Provided by the SAO/NASA Astrophysics Data System}
}
```

If you used the included `wabs` absorption model, plese cite:
for the wabs model (in xspec): Morrison & McCammon (1983; ADS link: 
https://ui.adsabs.harvard.edu/abs/1983ApJ...270..119M/abstract)
```
@ARTICLE{1983ApJ...270..119M,
       author = {{Morrison}, R. and {McCammon}, D.},
        title = "{Interstellar photoelectric absorption cross sections, 0.03-10 keV.}",
      journal = {\apj},
     keywords = {Absorption Cross Sections, Cosmic Dust, Interstellar Chemistry, Interstellar Matter, X Ray Astronomy, Abundance, Electrophotometry, Ionization, Molecules, Astrophysics},
         year = 1983,
        month = jul,
       volume = {270},
        pages = {119-122},
          doi = {10.1086/161102},
       adsurl = {https://ui.adsabs.harvard.edu/abs/1983ApJ...270..119M},
      adsnote = {Provided by the SAO/NASA Astrophysics Data System}
}
```
for the specific parameter value for the hydrogen column density:
McCammon et al. (2002; ADS link:
https://ui.adsabs.harvard.edu/abs/2002ApJ...576..188M/abstract), 
used for the Athena X-IFU background model
```
@ARTICLE{2002ApJ...576..188M,
       author = {{McCammon}, D. and {Almy}, R. and {Apodaca}, E. and {Bergmann Tiest}, W. and {Cui}, W. and {Deiker}, S. and {Galeazzi}, M. and {Juda}, M. and {Lesser}, A. and {Mihara}, T. and {Morgenthaler}, J.~P. and {Sanders}, W.~T. and {Zhang}, J. and {Figueroa-Feliciano}, E. and {Kelley}, R.~L. and {Moseley}, S.~H. and {Mushotzky}, R.~F. and {Porter}, F.~S. and {Stahle}, C.~K. and {Szymkowiak}, A.~E.},
        title = "{A High Spectral Resolution Observation of the Soft X-Ray Diffuse Background with Thermal Detectors}",
      journal = {\apj},
     keywords = {Instrumentation: Detectors, Instrumentation: Spectrographs- Galaxies: Intergalactic Medium, Space Vehicles: Instruments, X-Rays: Diffuse Background, X-Rays: ISM, Astrophysics},
         year = 2002,
        month = sep,
       volume = {576},
       number = {1},
        pages = {188-203},
          doi = {10.1086/341727},
archivePrefix = {arXiv},
       eprint = {astro-ph/0205012},
 primaryClass = {astro-ph},
       adsurl = {https://ui.adsabs.harvard.edu/abs/2002ApJ...576..188M},
      adsnote = {Provided by the SAO/NASA Astrophysics Data System}
}
```

For the line energies in the `line_eng_ion` dictionary near the top of
`calc_minsb.py`: CLOUDY v7.02, Ferland et al. (1998; ADS link:
https://ui.adsabs.harvard.edu/abs/1998PASP..110..761F/abstract)
```
@ARTICLE{1998PASP..110..761F,
       author = {{Ferland}, G.~J. and {Korista}, K.~T. and {Verner}, D.~A. and {Ferguson}, J.~W. and {Kingdon}, J.~B. and {Verner}, E.~M.},
        title = "{CLOUDY 90: Numerical Simulation of Plasmas and Their Spectra}",
      journal = {\pasp},
         year = 1998,
        month = jul,
       volume = {110},
       number = {749},
        pages = {761-778},
          doi = {10.1086/316190},
       adsurl = {https://ui.adsabs.harvard.edu/abs/1998PASP..110..761F},
      adsnote = {Provided by the SAO/NASA Astrophysics Data System}
}
``` 

For the line selection in the `line_eng_ion` dictionary near the top of
`calc_minsb.py`: Bertone et al. (2010; 
ADS: https://ui.adsabs.harvard.edu/abs/2010MNRAS.407..544B/abstract)
```
@ARTICLE{2022MNRAS.514.5214W,
       author = {{Wijers}, Nastasha A. and {Schaye}, Joop},
        title = "{The warm-hot circumgalactic medium around EAGLE-simulation galaxies and its detection prospects with X-ray-line emission}",
      journal = {\mnras},
     keywords = {galaxies: formation, galaxies: groups: general, galaxies: haloes, large-scale structure of Universe, X-rays: galaxies, Astrophysics - Astrophysics of Galaxies, Astrophysics - Cosmology and Nongalactic Astrophysics},
         year = 2022,
        month = aug,
       volume = {514},
       number = {4},
        pages = {5214-5237},
          doi = {10.1093/mnras/stac1580},
archivePrefix = {arXiv},
       eprint = {2108.04847},
 primaryClass = {astro-ph.GA},
       adsurl = {https://ui.adsabs.harvard.edu/abs/2022MNRAS.514.5214W},
      adsnote = {Provided by the SAO/NASA Astrophysics Data System}
}
```

For the Fe L-shell line energies in `savetable_sbmin` in `calc_minsb.py`: 
CLOUDY v17.01, Ferland et al. (2017; ADS link: 
https://ui.adsabs.harvard.edu/abs/2017RMxAA..53..385F/abstract)
```
@ARTICLE{2017RMxAA..53..385F,
       author = {{Ferland}, G.~J. and {Chatzikos}, M. and {Guzm{\'a}n}, F. and {Lykins}, M.~L. and {van Hoof}, P.~A.~M. and {Williams}, R.~J.~R. and {Abel}, N.~P. and {Badnell}, N.~R. and {Keenan}, F.~P. and {Porter}, R.~L. and {Stancil}, P.~C.},
        title = "{The 2017 Release Cloudy}",
      journal = {\rmxaa},
     keywords = {atomic processes, galaxies: active, methods: numerical, molecular processes, radiation mechanisms: general, Astrophysics - Astrophysics of Galaxies},
         year = 2017,
        month = oct,
       volume = {53},
        pages = {385-438},
          doi = {10.48550/arXiv.1705.10877},
archivePrefix = {arXiv},
       eprint = {1705.10877},
 primaryClass = {astro-ph.GA},
       adsurl = {https://ui.adsabs.harvard.edu/abs/2017RMxAA..53..385F},
      adsnote = {Provided by the SAO/NASA Astrophysics Data System}
}
```

For the selection of those Fe L-shell lines: Ploeckinger & Schaye (2020;
ADS: https://ui.adsabs.harvard.edu/abs/2020MNRAS.497.4857P/abstract)
```
@ARTICLE{2020MNRAS.497.4857P,
       author = {{Ploeckinger}, Sylvia and {Schaye}, Joop},
        title = "{Radiative cooling rates, ion fractions, molecule abundances, and line emissivities including self-shielding and both local and metagalactic radiation fields}",
      journal = {\mnras},
     keywords = {radiative transfer, ISM: general, intergalactic medium, galaxies: ISM, Astrophysics - Astrophysics of Galaxies},
         year = 2020,
        month = oct,
       volume = {497},
       number = {4},
        pages = {4857-4883},
          doi = {10.1093/mnras/staa2172},
archivePrefix = {arXiv},
       eprint = {2006.14322},
 primaryClass = {astro-ph.GA},
       adsurl = {https://ui.adsabs.harvard.edu/abs/2020MNRAS.497.4857P},
      adsnote = {Provided by the SAO/NASA Astrophysics Data System}
}
```

If you used the plotting routines, please acknowledge Paul Tol, who put
together the colorblind-friendly color schemes. See 
https://personal.sron.nl/~pault/
for background information and best usage of the schemes.





















