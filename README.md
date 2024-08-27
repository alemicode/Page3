# Pagination in Android


This repository demonstrates various approaches to implementing pagination in Android using different data sources. Below is an overview of how to set up pagination with Room only, Retrofit only, and a combination of Room and Retrofit.



## Room Only Pagination
### Overview
This approach uses Room as the local data source to handle pagination. It includes a PagingSource that queries paginated data directly from the Room database. This setup is ideal for static or pre-loaded datasets, managing pagination entirely within the local database.

#### Implementation
- Entity: Define your data model as a Room entity.
- DAO: Implement methods to fetch paginated data from the Room database.
- PagingSource: Create a custom PagingSource to handle pagination logic and interact with the DAO.
- Repository: Expose the PagingSource to the ViewModel for UI consumption.
- ViewModel: Observe the paginated data from the repository.

## Usage

```kotlin
@Entity(tableName = "unsplash_images")
data class UnsplashImageEntity(
    @PrimaryKey val id: String,
    val url: String,
    val description: String
)

@Dao
interface UnsplashImageDao {
    @Query("SELECT * FROM unsplash_images ORDER BY id ASC LIMIT :limit OFFSET :offset")
    suspend fun getImagesWithPagination(offset: Int, limit: Int): List<UnsplashImageEntity>

    @Insert(onConflict = OnConflictStrategy.REPLACE)
    suspend fun insertAll(images: List<UnsplashImageEntity>)

    @Query("DELETE FROM unsplash_images")
    suspend fun clearAll()
}

class UnsplashImagePagingSource(
    private val unsplashImageDao: UnsplashImageDao
) : PagingSource<Int, UnsplashImageEntity>() {
    override suspend fun load(params: LoadParams<Int>): LoadResult<Int, UnsplashImageEntity> {
        // Implementation here
    }
}

```


Certainly! Here’s the revised content formatted in the style suitable for a README file:

Pagination in Android
This repository demonstrates various approaches to implementing pagination in Android using different data sources. Below is an overview of how to set up pagination with Room only, Retrofit only, and a combination of Room and Retrofit.

Room Only Pagination
Overview
This approach uses Room as the local data source to handle pagination. It includes a PagingSource that queries paginated data directly from the Room database. This setup is ideal for static or pre-loaded datasets, managing pagination entirely within the local database.

## Implementation
- Entity: Define your data model as a Room entity.
- DAO: Implement methods to fetch paginated data from the Room database.
- PagingSource: Create a custom PagingSource to handle pagination logic and interact with the DAO.
- Repository: Expose the PagingSource to the ViewModel for UI consumption.
- ViewModel: Observe the paginated data from the repository.
## Example
kotlin
```kotlin
@Entity(tableName = "unsplash_images")
data class UnsplashImageEntity(
    @PrimaryKey val id: String,
    val url: String,
    val description: String
)

@Dao
interface UnsplashImageDao {
    @Query("SELECT * FROM unsplash_images ORDER BY id ASC LIMIT :limit OFFSET :offset")
    suspend fun getImagesWithPagination(offset: Int, limit: Int): List<UnsplashImageEntity>

    @Insert(onConflict = OnConflictStrategy.REPLACE)
    suspend fun insertAll(images: List<UnsplashImageEntity>)

    @Query("DELETE FROM unsplash_images")
    suspend fun clearAll()
}

class UnsplashImagePagingSource(
    private val unsplashImageDao: UnsplashImageDao
) : PagingSource<Int, UnsplashImageEntity>() {
    override suspend fun load(params: LoadParams<Int>): LoadResult<Int, UnsplashImageEntity> {
        // Implementation here
    }
}
```
## Retrofit Only Pagination
#### Overview
This approach uses Retrofit to fetch paginated data from a remote source. It involves a PagingSource that queries data from a network API. This setup is suitable for loading data from a remote server with pagination.

## Implementation
- API Interface: Define Retrofit endpoints for fetching paginated data.
- PagingSource: Implement a PagingSource to fetch data from the network using Retrofit.
- Repository: Provide the PagingSource to the ViewModel.
- ViewModel: Observe and manage paginated data from the repository.
```kotlin
interface UnsplashApi {
    @GET("images")
    suspend fun searchImages(@Query("query") query: String, @Query("perPage") perPage: Int): Response<UnsplashImageResponse>
}

class SearchPagingSource(
    private val unsplashApi: UnsplashApi,
    private val query: String
) : PagingSource<Int, UnsplashImageEntity>() {
    override suspend fun load(params: LoadParams<Int>): LoadResult<Int, UnsplashImageEntity> {
        // Implementation here
    }
}
```

## Room + Retrofit Pagination
# Overview
This approach combines Room and Retrofit to handle pagination. It fetches data from a remote source using Retrofit and caches it locally in Room. A RemoteMediator manages loading data from the network and saving it into the Room database, handling both local and remote data sources.

## Implementation
- Entity: Define your data model as a Room entity.
- DAO: Implement methods for inserting and querying data.
- RemoteMediator: Create a RemoteMediator to fetch data from Retrofit and store it in Room.
- Repository: Set up a Pager with the RemoteMediator to handle pagination.
- ViewModel: Observe the paginated data from the - 
- repository.
Example
kotlin
``` kotlin
class UnsplashRemoteMediator(
    private val unsplashApi: UnsplashApi,
    private val unsplashImageDao: UnsplashImageDao
) : RemoteMediator<Int, UnsplashImageEntity>() {
    override suspend fun load(
        loadType: LoadType,
        state: PagingState<Int, UnsplashImageEntity>
    ): MediatorResult {
        // Implementation here
    }
}

class UnsplashImageRepository(
    private val unsplashApi: UnsplashApi,
    private val unsplashImageDao: UnsplashImageDao
) {
    fun getPagedImages() = Pager(
        config = PagingConfig(pageSize = 20, enablePlaceholders = false),
        remoteMediator = UnsplashRemoteMediator(unsplashApi, unsplashImageDao),
        pagingSourceFactory = { unsplashImageDao.getPagedImages() }
    ).flow
}
```
## Conclusion
 This repository provides a comprehensive approach to handling pagination in Android applications using Room, Retrofit, or a combination of both. Choose the method that best fits your application's data source and requirements.

For more detailed instructions, refer to the implementation sections and adapt the provided examples to fit your specific use case.
