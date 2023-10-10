## Augmented Tycho - HYG (AT-HYG)

### Rationale

HIPPARCOS (and thus HYG) is getting a little dated, while Gaia (its ultimate replacement for many purposes) is absolutely gigantic -- 1.3 billion records -- and too large for many general-purpose applications. For this update, I wanted to accomplish the following:

1. Incorporate Gaia results into the old but popular HYG dataset.
2. Use a well-defined modern star catalog as a primary basis for IDs, rather than the somewhat _ad hoc_ system in HYG.
3. Create a larger database than HYG that is suitable for today's faster systems, but still small enough to be manageable in many applications.

I chose the Tycho-2 catalog for this purpose. The Tycho-2 catalog's size (~2.5M records) and coverage (essentially complete to V = 11, mostly complete to V = 11.5) makes it suitable for many current applications. It's large enough to be comprehensive for all but the deepest charts, but small enough to be manageable in fairly simple applications. 

Tycho-2 (released in 2000) is only slightly younger than HIPPARCOS, so the idea was to combine it with the newer Gaia DR3 results (2020-2022) whenever feasible. This gives us high-quality distance information for over 2M stars instead of the lower-precision results from HIPPARCOS for 0.1M. Adding Gaia results to Tycho-2 gives an "Augmented" Tycho-2 dataset (AT), which is the basis for most stars in the new catalog.

By merging AT with HYG, the "classical" IDs and names for a large number of Tycho / Gaia stars are also available. The result is AT-HYG. 

### Download Format

The full catalog, even when compressed, is too large for simple hosting in this repository. It is currently split into 2 components, which should be downloaded, uncompressed, and concatenated, e.g. (Linux command for version 2.0):

`cat athyg_v20-1.csv athyg_v20-2.csv > athyg_v20.csv`

The full CSV can then be imported into the database tool of your choosing.

### Current Version: 

The current version of AT-HYG is version v2.0 (v2/athyg_v20-*.csv.gz). 

#### Changes from version 1.x:

Version 2.0 adds 3D velocity data to the data in version 1.1.

The new fields are:

* `rv`: The radial velocity in km/sec.
* `rv_src`: The source for the radial velocity.
* `pm_ra`, `pm_dec`: Proper motion in right ascension and declination respectively, in milliarcseconds per year. `pm_ra` has already been adjusted by cos(declination) to convert to true milliarcseconds.
* `pm_src`: The source for the proper motion.
* `vx`,`vy`,`vz`: The Cartesian coordinates for the velocity, in km/sec. The coordinate system is the same as for `x0`,`y0`,`z0`.

The source field names are:

* `G_R2`: Gaia data release 2
* `G_R3`: Gaia data release 3
* `T`: Tycho-2
* `HIP`: HIPPARCOS
* `HYG`: Source catalogs for HYG values. For proper motions this is normally HIP, but for radial velocities, it can be Yale BSC, Gliese, or the Wilson Evans Batten (Henry Draper cross-reference) catalog of radial velocities. These were not historically differentiated during the HYG build, but in general the newer W.E.B. catalog data was preferred.
* `GJ`: Gliese (Gliese-Jahreiss)

##### Details on velocity calculations

The vast majority of proper motion data came from Gaia DR3, with HIP and Tycho-2 providing a few values, especially for stars too bright to have been measured by Gaia. A small number of HYG stars only in the Gliese catalog kept their proper motion values from that catalog.

Cartesian velocities were not calculated if either the distance or the proper motions were missing or otherwise unknown.

A large majority of radial velocities came from Gaia DR3, but about 20% of Tycho-2 stars lack a reliable Gaia radial velocity. As with proper motions, this includes bright stars in HYG that were too bright for Gaia to measure accurately, and inherited their radial velocity from HYG. As noted, this HYG radial velocity comes from various sources of generally acceptable (bthough lower precision than Gaia) radial velocity values.

When the radial velocity was missing or equal to zero, the Cartesian velocities were still calculated, but assuming no radial component. This makes those particular values accurate only for short time frames.

##### Comparison to HYG

The HYG catalog lists proper motions and radial velocities in the same units as AT-HYG (milliarcsec/year and km/sec respectively), but has the Cartesian velocities vx, vy, and vz in parsecs per year. AT-HYG uses kilometers per second, in part to reduce the space needed for figures in CSV format (the figures in pc/year have 5 or 6 leading zeroes) and in part to make direct comparisons to the radial velocity simple. The conversion factor for converting km/sec to pc/year is 1 km/sec = 1.02269 E-6 pc/year.

Apart from the difference in units for vx, vy, and vz, and a few slightly different field names, AT-HYG 2.0 is almost a drop-in replacement for HYG 3.x for many applications -- in particular, for any application that needs only the stars' catalog IDs, historical IDs (like Henry Draper or traditional names), magnitudes, positions, and velocities. The fields missing from AT-HYG that are present in HYG are:

* B-V color index
* Spectral type
* Variable star IDs and approximate magnitude ranges
* Explicit identification of components in multiple stars (in terms of a "base ID" representing the primary and a component ID)

Later versions of AT-HYG will focus on adding more of these fields.
### Other Versions:

See version-info.md for more details on previous versions of the catalog.