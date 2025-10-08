# Spatiotemporal Asset Catalog (STAC)

Explore the components of a [STAC](https://stacspec.org/en), and create one from scratch using subsets of SNAP's CF-compliant data.

### Setup & Run

To run the notebook, you will need an environment with `pystac` installed.

Unzip `cmip6_subsets.zip` and replace the filepaths in the bottom section of the `datacube_to_STAC.ipynb` notebook with your local filepaths. Run the notebook and investigate the outputs. (Alternatively, just view the outputs in the `/stac` and `/cmip6_stac` directories.)

### `pyegoapi`

Use the `example-config.yml` to set up a local instance of the `pygeoapi` that points to our STAC. Follow instructions in the `/pygeoapi/README.md` to get started. 



## Notes

- The STAC is basically a collection of JSON files that can be organized into a data hierarchy (`Catalog` > `Collection` > `Item` > `Asset`) by internal references (i.e., defined parent/child relationships present in the JSON itself). 
- The STAC can be made browseable & searchable by many different apps due to its standardized structure; a "STAC API" is an app that can host the STAC catalog and facilitate queries and validations.
- The STAC API specification is formally defined using the OpenAPI specification; all capabilities, endpoints, data models, and parameters of a STAC API are described in a the OpenAPI format, which allows automatic generation of client libraries, documentation, and testing tools (see [Swagger](https://swagger.io/tools/swagger-ui/) and [Redoc](https://redocly.com/redoc) for two examples - these are available out-of-the-box in `pygeoapi` and are also used by [FastAPI](https://fastapi.tiangolo.com/).
- The `Asset` can be an actual file on the local filesystem, a link to files in cloud storage, or potentially just internally meaningful references to data (like a Rasdaman coverage ID).
- As shown in this notebook, it’s not super difficult to take an `xarray.Dataset` (derived from NetCDF or `zarr` files) and create our own STAC from scratch.


- ## Why would SNAP use a STAC?
- Benefits of a STAC are largely the benefits of having well-structured, standardized metadata records; a STAC potentially makes validation and search much more streamlined. There are of course added benefits to using a standard that has been adopted by multiple open source applications, should one decide to use those applications.
- With a STAC, we could potentially answer questions we would be difficult answer now, without ever touching the data itself. These could be general questions about our holdings like:
  - Which of our datasets contain temperature as a variable?
  - Which of our datasets are finer than 10km resolution?
  - Which of our datasets cover the time range 1950-1990?
Or more specific questions about holdings like:
  - At this point / polygon, show me all of the datasets we have?
  - At this point / polygon, show me any datasets that have precipitation data covering all years from 2010-2070, where we have at least 3 models to compare, at finer than 20km resolution?
Basically we can ask general questions of the entire data catalog instead of questions about specific coverages. We don’t need a lot of _a priori_ knowledge in order to start asking questions of the entire database. We can do this with Rasdaman coverages, but the workflow is more complex: we would need to list the coverages on the server, query the metadata for each coverage individually, parse the structure of each coverage individually, and gather values from the metadata (if they exist) for validation / filtering based on the query. Since many coverages do not have complete metadata, this would be quite the task.

## Other users
- [USGS GeoDataPortal uses a STAC](https://api.water.usgs.gov/gdp/pygeoapi/stac?f=html) for their `zarr` stores, which live on S3 buckets. They use `pygeoapi` as their STAC API which permits simple browsing of their assets, and display of the asset metadata. It’s unclear if they really use the query functionality of `pygeoapi` to request and return data, or are relying on users to grab the storage location after browsing (for example, browsing the catalog to find the file location like: `s3://mdmf/gdp/alaska_et_2020_era-interim_reanalysis.zarr`) and access it themselves by downloading the whole file or accessing chunks programmatically via `s3fs`/`fsspec` and `xarray`. 



