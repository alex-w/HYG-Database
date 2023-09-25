## Augmented Tycho - HYG (AT-HYG)

### Rationale

HIPPARCOS (and thus HYG) is getting a little dated, while Gaia (its ultimate replacement for many purposes) is absolutely gigantic -- 1.3 billion records -- and too large for many general-purpose applications. For this update, I wanted to accomplish the following:

1. Incorporate Gaia results into the old but popular HYG dataset.
2. Use a well-defined modern star catalog as a primary basis for IDs, rather than the somewhat _ad hoc_ system in HYG.
3. Create a larger database than HYG that is suitable for today's faster systems, but still small enough to be manageable in many applications.

I chose the Tycho-2 catalog for this purpose. The Tycho-2 catalog's size (~2.5M records) and coverage (essentially complete to V = 11, mostly complete to V = 11.5) makes it suitable for many current applications. It's large enough to be comprehensive for all but the deepest charts, but small enough to be manageable in fairly simple applications. 

Tycho-2 (released in 2000) is only slightly younger than HIPPARCOS, so the idea was to combine it with the newer Gaia DR3 results (2020-2022) whenever feasible. This gives us high-quality distance information for over 2M stars instead of the lower-precision results from HIPPARCOS for 0.1M. By merging this data set with HYG, the "classical" IDs and names for a large number of Tycho / Gaia stars are also available. 

### Download Format

The full catalog, even when compressed, is too large for simple hosting in this repository. It is currently split into 2 components, which should be downloaded, uncompressed, and concatenated, e.g. (Linux command for version 1.0):

`cat athyg_v1_0-1.csv athyg_v1_0-2.csv > athyg_v1_0.csv`

The full CSV can then be imported into the database tool of your choosing.

### Current Version: v1.1

The current version of AT-HYG is version v1.1 (v1/athyg_v101-*.csv.gz). All v1.x versions have similar schemas, but there are minor to moderate changes in the data.

#### Changes from v1.0 to v1.1

No new stars were added, but two significant changes to data were made:

1. Improvements to Gaia distance data. Approximately 10K stars were identified in SIMBAD that lacked Gaia DR3 distances in AT-HYG 1.0 and were relatively bright (V < 10.0). 
These stars were assigned either a Gaia DR2 or DR3 parallax when found in SIMBAD, with DR3 being preferred.
Of these, 7726 got new Gaia DR3 values and 813 got Gaia DR2 values.
2. Constellation IDs are now available for all 2.4M+ stars. They were added by the same process that updated HYG v3.6+ to high-accuracy constellation IDs. The HYG
constellations were taken as canonical, so only non-HYG stars were updated.

The first change also led to a change in the `dist_src` field. For Gaia distances, this can be either `GAIA_DR2` or `GAIA_DR3` depending on which Gaia data release
was used.  Gaia DR2 data was used only for stars that did not have a valid Gaia DR3 value for parallax (in this release, 813 stars)

### Version 1.0 Data Format

Version 1.0 has the following data fields:

1. `id`: A numeric ID for each star, after sorting all entries by right ascension.
2. `tyc`: The Tycho-2 ID, with leading zeros removed from the first and second portion (for consistency with Gaia linking tables)
3. `gaia`: The Gaia Data Release 3 ID.
4. `hyg`: The HYG main catalog ID from HYG v3.
5. `hip`: The HIPPARCOS ID, from HYG if known, otherwise Tycho-2.
6. `hd`: The Henry Draper (HD) catalog ID, from HYG if known, otherwise Tycho-2.
7. `hr`: The Harvard / Yale Bright Star Catalog ID, from HYG.
8. `gl`: The Gliese ID, from HYG
9. `bayer`: The Bayer (Greek letter) designation, from HYG
10. `flam`: The Flamsteed number, from HYG
11. `con`: The three-letter constellation abbreviation, from HYG
12. `proper`: A proper name for the star, from HYG
13. `ra`: Right ascension (epoch + equinox 2000.0), in hours, from HYG or TYC
14. `dec`: Declination (epoch + equinox 2000.0), in degrees, from HYG or TYC
15. `pos_src`: Indicator of source for the position fields `ra` and `dec` (see below)
16. `dist`: Distance from Sol in parsecs. From Gaia if known, otherwise HYG.
17. `x0`
18. `y0`
19. `z0`: These three fields are Cartesian coordinates. The directions are such that x is towards RA 0, Dec 0, y towards RA 6 hr., Dec 0, and z towards Dec 90 degrees.
20. `dist_src`: Indicator of source for the distance fields `dist`, `x0`, `y0`, `z0` (see below). `x0`, `y0`, and `z0` also depend on `ra` and `dec`, so they will also be determined by the position source. An extremely common combination is raw distance from Gaia but the position from TYC.
21. `mag`: V or VT magnitude for the star
22. `absmag`: Corresponding absolute magnitude
23. `mag_src`: Indicator of source for the magnitude field `mag` (see below). `absmag` depends on both apparent magnitude and distance, so may be determined by values from two sources.

Since different versions of the DB may add or remove records, the `id` value is necessarily associated with a specific version. 

### Previous Versions: v0.x

Versions v0.x were public alpha releases. Version v0.1 was mostly to make sure data processing was working as intended. It was not publicly released.

Versions v0.2 and v0.3 have the same field structure as v1.0, but significantly changed the way many of the HYG stars, especially from Gliese, were merged. 

These versions are deprecated and should be replaced with v1.x.

### Current Version (v1.x) Source Data And Assembly:

The data sources for version 1.1 are:

- The full Tycho-2 catalog available from https://cdsarc.cds.unistra.fr/viz-bin/cat/I/259#/browse .
- The first Tycho-2 Supplemental catalog, available from https://cdsarc.cds.unistra.fr/ftp/cats/I/259/suppl_1.dat.gz.
- A link table catalog between Tycho-2 and Henry Draper, generated from the catalog at https://cdsarc.cds.unistra.fr/viz-bin/qcat?J/A+A/386/709.
- A link table catalog between Tycho-2 and Gaia DR3. I used the query facility at https://gaia.aip.de/query/. Details of the specific query are in the details_v1.md file.
- A collection of relatively bright stars (V < 10.0) that were missing valid Gaia distances after the first 4 catalogs above were linked. These were queried in SIMBAD and Gaia information added for ones that had a valid Gaia parallax reference.
- HYG v3.6.1, from this site. 
- A collection of data for Gliese IDs from SIMBAD (https://simbad.cds.unistra.fr/simbad/) that linked Gliese IDs to Tycho-2 and Gaia DR3 IDs when no other linking information was available. The list of Gliese IDs was taken directly from the HYG catalog and matched to other catalogs via automated queries to SIMBAD.

The first five catalogs, after correcting a few cross-reference errors, consitute the Augmented Tycho or AT catalog. This catalog contains every valid (both position and magnitude are present and within reasonable bounds) Tycho-2 star from the main and first supplemental Tycho-2 catalogs, along with the cross-reference IDs to Henry Draper and Gaia and, when available, Gaia DR2 or DR3 distances.

Linking the AT catalog to HYG v3.6.1 via the HIPPARCOS and Henry Draper IDs in AT gives most HYG cross-references, which add HYG data such as proper name, Bayer and Flamsteed IDs, and spectral type to the catalog for the linked stars.

Finally, most unlinked HYG records (no HIPPARCOS or Henry Draper IDs) can be linked via the Gliese ID and a suitable Tycho-2 or Gaia DR3 ID found via SIMBAD. The list of Gliese IDs included all single and "A" multiple star components from HYG, as well as a few selected secondary or tertiary stars when they were well-separated and well-characterized in all catalogs.

The end result, after fixing a few more cross-reference errors and validating subsets of the data against other lists -- e.g., the Wikipedia article on nearest stars, https://en.wikipedia.org/wiki/List_of_nearest_stars_and_brown_dwarfs -- is the AT-HYG catalog.

### Additional Details

Information about how this data set was generated are in the "details" files:

- `details_v0.md`: Information about the alpha (v0.x) releases, which covers some of the core aspects of the data processing used in later releases
- `details_v1.md`: information about the current (v1.x) releases
- `details_v1_errata.md`: Information about various source catalog errors found, and corrected in, v1.0


#### Source Indicator meanings:

Source indicator fields ("*_src") identify the data source used for a value. The indicator fields apply to fields for the following data:

- Distances
- Positions (equatorial coordinates from Earth)
- Apparent Magnitudes

Note that some fields have dependencies on multiple sources: for example, the Cartesian coordinates depend on both the position and distance, and absolute magnitudes depend on distance and (apparent) magnitudes.

- `TYC`: Fields are from Tycho-2. [Positions or magnitudes]
- `GAIA`: Fields are from Gaia. [Distances only, v1.0]
- `GAIA_DR2`, `GAIA_DR3`: Fields from Gaia and the specified data release (DR2 or DR3) [Distances only, v1.1+]
- `HIP`: Fields are from HIPPARCOS. [Positions, distances, or magnitudes]
- `HIP_X`: Fields are from HIPPARCOS overriding a known Tycho-2 value. This is only for the special case where the Tycho-2 star position was a "non-mean" position and hence not having the same epoch as the rest of the data. [Positions only]
- `GLIESE`: Fields are from Gliese. [Positions, distances, or magnitudes]
- `OTHER`: Fields are from another source. Currently applies only to the special entry for Sol. [Positions, distances, or magnitudes]
- `NONE`: No valid source of this data; uncommon [Distances only]

#### Data By Source Types

Here are the numbers of stars for each combination of possible source types in v1.0. The values for v1.1 are similar (most previous GAIA distances became GAIA_DR3,
and a few thousand NONE distances became GAIA_DR2 or GAIA_DR3). 

The most common entry in AT-HYG has a position and brightness (apparent magnitude) from Tycho-2 and a distance from Gaia. 
All told, the very large majority of stars have a Gaia DR3 distance, including about 90% of HIPPARCOS entries (some of which are too bright to have good Gaia DR3 data at the moment):


|Position|Distance|Magnitude|Total Count
|--------|--------|---------|-----
TYC      | GAIA   | TYC     | 2367088
TYC      | GAIA   | HIP     | 104668
TYC      | NONE   | TYC     | 66116
TYC      | HIP    | HIP     | 11897
GLIESE   | GAIA   | GLIESE  | 952
HIP_X    | GAIA   | HIP     | 694
TYC      | NONE   | HIP     | 512
HIP_X    | HIP    |HIP      | 196
GLIESE   | GLIESE | GLIESE  | 30
TYC      | GLIESE | HIP     | 9
HIP_X    | NONE   | HIP     | 3
OTHER    | OTHER  | OTHER   | 1