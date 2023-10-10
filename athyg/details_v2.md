## Additional Details

This goes pretty deep into some data-quality and data-processing weeds. Feel free to skip if you don't want the full story; just note that issues described here will likely affect later versions of the database.

### Data selection criteria

The main goal for AT-HYG was slightly different from that for HYG.

For HYG, it was to get, as completely as possible, all the entries from the 3 source catalogs into one merged data set. Since essentially all stars in YBSC were also in HIPPARCOS, the outliers -- the ones that were mostly not identified elsewhere -- were from Gliese. And an explicit part of compiling HYG was to get as many of those as possible, regardless of brightness or additional data about them.

For AT-HYG, it was to get, as completely as possible, accurate data (preferably from GAIA for distances) for as many stars as possible above a specific magnitude limit, while keeping the data set size manageable. The Tycho-2 limit of V ~ 12 is the result. Stars fainter than that limit are subject to being dropped from AT-HYG if they cannot be matched accurately to GAIA for distances or Tycho-2 or GAIA for positions. 

A related condition is because the treatment of multiple stars varies significantly between catalogs, AT-HYG focuses on getting accurate data for _at least_ one star in any given star system in any catalog, but unlike HYG, it does not attempt to get _all_ of the stars in a system, but instead tries to get the most accurate data it can get for at least one. In practice, this is almost always the brightest or "A" component in a multiple-star system.

### Version-specific notes:

#### Generating the Augmented Tycho (AT) catalog v2.0

The acceptance criteria for the Augmented Tycho (AT) catalog (in any version) are:

1. AT contains _every_ star in the Tycho-2 catalog with valid Tycho-2 visual magnitude data. Only 20 of the 2.5M+ records in Tycho-2 are missing a valid visual magnitude.
2. AT contains _as many stars_ with good quality distance information as possible, with Gaia DR3 values preferred, and Gaia DR2 or HIPPARCOS acceptable if Gaia DR3 is not available.
3. AT contains _accurate cross-reference IDs_ for every star that has an available reference: 
    * Tycho-2 (the base catalog), 
    * Gaia, with DR3 values preferred.
    * Other major catalogs, predominantly HIPPARCOS and Henry Draper.

The version of AT used for AT-HYG v2.0 is AT v2.0. The primary difference between AT 1.x and AT 2.x is the addition of velocity data (proper motions and radial velocities). The primary Gaia to Tycho-2 cross-reference query for AT v2.0 was almost identical to the version for v1.x, except for adding the velocity data:

`select g."source_id", 
    t.original_ext_source_id, 
    g.ra, 
    g.dec, 
    g.parallax,
    g.pm_ra,
    g.pm_dec,
    g.radial_velocity
from "gaiadr3"."gaia_source_lite" g 
inner join "gaiaedr3"."tycho2tdsc_merge_best_neighbour" t on g."source_id"=t."source_id";
`

The cross-referencing to other catalogs (such as the Henry Draper catalog) followed the same process as for AT v1.x; see the AT v1.x construction notes in details_v1.md for more information.

As before, AT v2.0 contains _every_ star in the Tycho-2 catalog with valid Tycho-2 visual magnitude data (only 20 stars out of over 2.5 million dropped, because of invalid magnitude values), along with its HIP ID, HD ID, Gaia DR3 ID, and Gaia distance/parallax, proper motions, and radial velocities when these values are known. It also contains computed values for the Cartesian velocity components vx, vy, and vz that can be used directly whenever possible.

#### Generating the merged Augmented Tycho-HYG (AT-HYG) catalog

This followed an almost identical process to the process described in details_v1.md.

The current starting points for AT-HYG v2.0 are:

* AT v2.0 (described above)
* HYG v3.7.

HYG v3.7 is a minor update to the version used for AT-HYG v1.1 (HYG v3.6.1). It adds some data for Yale Bright Star Catalog stars that was missing or incorrect in previous versions.

The only significant difference in the AT-HYG build was to add a step to check AT for missing proper motion and/or radial velocity data, and substitute values from HYG when known. If there was a change to the AT velocity data, the build process recalculated the vx, vy, and vz values obtained from the proper motions and radial velocity, and updated the AT-HYG catalog with these new values.

### Data quality

Data quality issues are largely the same as in details_v1.md, except for issues specific to proper motion and radial velocity data.

There are two items for AT-HYG v2.0 that are worth noting:

1. There is a minor deficiency in the build for HYG that affects the "source" tracking for some of the velocity data in HYG. In AT-HYG, one priority has been to label the provenance of all data as closely as possible, so it is possible at a glance to see where the data for a specific figure (such as distance or absolute magnitude) comes from. In HYG, this was well-tracked for IDs (hence the name HYG for Hipparcos + Yale + Gliese), as well as for data where there was a clear quality hierarchy. For example, if there was a HIP parallax and a Yale BSC or Gliese parallax, the HIP parallax was chosen without hesitation. The same was generally true for proper motions in HYG. On the other hand, for radial velocities in HYG, there were no values from HIPPARCOS, so they are more of a grab bag. The main source was a somewhat niche catalog oriented specifically towards collecting radial velocities (Wilson Evans Batten), but some came from Yale, and some others from Gliese. As a result, the origin of radial velocities (and, to a rather lesser extent, proper motions) in HYG is not always clear. This is why they were given a "HYG" source in AT-HYG rather than something more specific like "GLIESE"/"GJ". 

Untangling this was out of scope for AT-HYG. I spot checked a few of the HYG radial velocities in SIMBAD and, in general, they are of acceptable quality, just generally lower precision than ones from Gaia.

2. In AT-HYG v1.01 and later, there is a custom list of Tycho-2 stars that were missing a Gaia-Tycho 2 link from the data files I worked with, but which could be looked up in SIMBAD and correctly identified there. This list (~10K stars) only contains the associated Gaia parallax data (no Gaia velocities). As a result, these stars do not currently have radial velocities (they generally have good proper motions from Tycho-2), and so their Cartesian velocity values are not especially accurate. 

Addressing this was considered to be fairly minor compared to getting the distances in the first place. Also, since these stars still generally have good proper motion data, any application that uses only the proper motions for (relatively short term) space motion calculations isn't affected; only long-term 3D motion calculations are an issue. Resolving this may be a candidate for later versions of AT-HYG. 

#### Possible additions in later releases

Here are some items that are possible additions:

1. Add BT (blue) magnitudes to Tycho's VT, giving us color index (on the BT and VT values) and also possibly an estimate of V magnitudes for Tycho stars, making magnitude values more consistent
2. Add spectral types directly from HD stars to ones that don't have them in HIP.
3. (lower priority) Add conventional variable star letter IDs (R-Z, RR-QZ, V numbers, etc.) when known. Combined with the other two, this makes AT-HYG close to a drop-in replacement for HYG, with all the data for all the stars in HYG replicated in AT-HYG.

