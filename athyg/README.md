## Augmented Tycho - HYG (AT-HYG)

### Rationale

HIPPARCOS (and thus HYG) is getting a little dated, while Gaia (its ultimate replacement for many purposes) is absolutely gigantic -- 1.3 billion records -- and too large for many general-purpose applications. For this update, I wanted to accomplish the following:

1. Incorporate Gaia results into the old but popular HYG dataset.
2. Use a well-defined modern star catalog as a primary basis for IDs, rather than the somewhat _ad hoc_ system in HYG.
3. Create a larger database than HYG that is suitable for today's faster systems, but still small enough to be manageable in many applications.

I chose the Tycho-2 catalog for this purpose. The Tycho-2 catalog's size (~2.5M records) and coverage (essentially complete to V = 11, mostly complete to V = 11.5) makes it suitable for many current applications. It's large enough to be comprehensive for all but the deepest charts, but small enough to be manageable in fairly simple applications. 

Tycho-2 (released in 2000) is only slightly younger than HIPPARCOS, so the idea was to combine it with the newer Gaia DR3 results (2020-2022) whenever feasible. This gives high-quality distance information for over 2M stars instead of the lower-precision results from HIPPARCOS for 0.1M. Adding Gaia results to Tycho-2 gives an "Augmented" Tycho-2 dataset (AT), which is the basis for most stars in the new catalog.

By merging AT with HYG, the "classical" IDs and names for a large number of Tycho / Gaia stars are also available. The result is AT-HYG. 

### Download Format

The full catalog, even when compressed, is too large for simple hosting in this repository. It is currently split into 2 components, which should be downloaded, uncompressed, and concatenated, e.g. (Linux command for version 2.1):

`cat athyg_v21-1.csv athyg_v21-2.csv > athyg_v21.csv`

The full CSV can then be imported into the database tool of your choosing.

### Current Version: 

The current version of AT-HYG is version v2.1 (v2/athyg_v21-*.csv.gz). 

#### Changes from version 2.0:

##### Color index ("ci") field

Version 2.1 adds a color index ("CI") field to the database. The color index comes either from Tycho-2 data ("mag_src" field = "T") or from HYG ("mag_src" field predominantly "HIP", a few "GJ" [Gliese] values). 

The main difference between the Tycho-2 data and the other sources is Tycho-2 used slightly different magnitude measurements. HIPPARCOS and the older HYG sources used the common 20th-century "B" and "V" filter types for their measurements. Tycho-2 used "BT" and "VT", which are close to, but not exactly the same as, B and V respectively. The "ci" field for the "T" magnitue source is therefore BT-VT rather than B-V.

For stars with either color index (B-V or BT-VT) close to 0, the differences are minimal, but for stars where VT is significantly smaller (i.e., brighter) than BT, the Tycho-2 BT-VT is noticeably larger than B-V. It is about 0.1 units higher for Sunlike (main sequence, spectral type G) stars and 0.25-0.3 for main-sequence M stars. 

##### Additional data from SIMBAD lookups

In previous versions of AT-HYG, a subset of HYG stars with Gliese IDs were run through data lookups in SIMBAD to get additional HYG-Tycho 2 cross-references and also improved parallax values from Gaia-DR2 or -DR3. This process was expanded to include proper motions and radial velocities in AT-HYG v2.1, adding a number of velocities that were missing or lower-quality in AT-HYG v2.0.


##### An (as in, one) incorrect radial velocity from SIMBAD

The star GJ 2077, in the initial release of v2.1, had an incorrect radial velocity as a result of a SIMBAD lookup returning a strange value. 

GJ 2077 has an RV of -140.0 km/s in the Gliese catalog, but SIMBAD returned a value of z = 2.188 (~0.8 c!) because of what seems to be a case of mistaken identity: a relatively recent publication (https://www.aanda.org/articles/aa/pdf/2010/10/aa14188-10.pdf) created a catalog of QSOs and classified it as a quasar with that redshift, instead of a white dwarf star. Shortly after originally releasing v2.1, I found the discrepancy and recalculated GJ 2077's Cartesian velocities with the older Gliese RV of -140.0 km/s and reuploaded. This did not seem to merit a distinct version of the catalog, so I left it as v2.1.

I spot-checked a few other large RVs and did not see anything else remotely as discrepant, so this version should be stable until the next significant change to types of data included in the catalog.

#### Details on velocity calculations

The vast majority of proper motion data came from Gaia DR3, with HIP and Tycho-2 providing a few values, especially for stars too bright to have been measured by Gaia. A small number of HYG stars only in the Gliese catalog kept their proper motion values from that catalog.

Cartesian velocities were not calculated if either the distance or the proper motions were missing or otherwise unknown.

A large majority of radial velocities came from Gaia DR3, but about 20% of Tycho-2 stars lack a reliable Gaia radial velocity. As with proper motions, this includes bright stars in HYG that were too bright for Gaia to measure accurately, and inherited their radial velocity from HYG. As noted, this HYG radial velocity comes from various sources of generally acceptable (bthough lower precision than Gaia) radial velocity values.

When the radial velocity was missing or equal to zero, the Cartesian velocities were still calculated, but assuming no radial component. This makes those particular values accurate only for short time frames.

#### Comparison to HYG

The HYG catalog lists proper motions and radial velocities in the same units as AT-HYG (milliarcsec/year and km/sec respectively), but has the Cartesian velocities vx, vy, and vz in parsecs per year. AT-HYG uses kilometers per second, in part to reduce the space needed for figures in CSV format (the figures in pc/year have 5 or 6 leading zeroes) and in part to make direct comparisons to the radial velocity simple. The conversion factor for converting km/sec to pc/year is 1 km/sec = 1.02269 E-6 pc/year.

Apart from the difference in units for vx, vy, and vz, and a few slightly different field names, AT-HYG 2.0 is almost a drop-in replacement for HYG 3.x for many applications -- in particular, for any application that needs only the stars' catalog IDs, historical IDs (like Henry Draper or traditional names), magnitudes, positions, and velocities. The fields missing from AT-HYG that are present in HYG are:

* Spectral type
* Variable star IDs and approximate magnitude ranges
* Explicit identification of components in multiple stars (in terms of a "base ID" representing the primary and a component ID)

Later versions of AT-HYG will focus on adding more of these fields.
### Other Versions:

See version-info.md for more details on previous versions of the catalog.