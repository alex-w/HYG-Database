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

### Current Version: v1.0

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

Versions v0.x are public alpha releases. They should be useful for many purposes, but they are not completely finished products and significant changes in a short time between versions are possible, even likely.

Version v0.1 was mostly to make sure data processing was working as intended. It was not publicly released.

Versions v0.2 and v0.3 have the same field structure as v1.0.

#### Source Indicator meanings:

Source indicator fields ("*_src") identify the data source used for a value. The indicator fields apply to fields for the following data:

- Distances
- Positions (equatoria coordinates from Earth)
- Apparent Magnitudes

Note that some fields have dependencies on multiple sources: for example, the Cartesian coordinates depend on both the position and distance, and absolute magnitudes depend on distance and (apparent) magnitudes.

- `TYC`: Fields are from Tycho-2. [Positions or magnitudes]
- `GAIA`: Fields are from Gaia. [Distances only]
- `HIP`: Fields are from HIPPARCOS. [Positions, distances, or magnitudes]
- `HIP_X`: Fields are from HIPPARCOS overriding a known Tycho-2 value. This is only for the special case where the Tycho-2 star position was a "non-mean" position and hence not having the same epoch as the rest of the data. [Positions only]
- `GLIESE`: Fields are from Gliese. [Positions, distances, or magnitudes]
- `OTHER`: Fields are from another source. Currently applies only to the special entry for Sol. [Positions, distances, or magnitudes]
- `NONE`: No valid source of this data; very uncommon [Distances only]

#### Additional Details

Information about how this data set was generated are in the "details" files:

- `details_v0.md`: Information about the alpha (v0.x) releases
- `details_v1.md`: information about the current (v1.0) release
- `details_v1_errata.md`: Information about various source catalog errors found, and corrected in, v1.0