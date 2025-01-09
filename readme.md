# golang-api

This API is based on the tutorial [*Developing a RESTful API with Go and Gin*](https://go.dev/doc/tutorial/web-service-gin)

## API

The API provides access to a store selling vintage records on vinyl. The main code that register all paths in the respective handler is:
```go
func main() {
	router := gin.Default()
	router.GET("/albums", getAlbums)
	router.GET("/albums/:id", getAlbumByID)
	router.POST("/albums", postAlbum)

	router.Run("localhost:8080")
}
```

## Data Structures

The data structure for this API will be an Album:
```go
type Album struct {
    ID     string `json:"id"`
    Title  string `json:"title"`
    Artist string `json:"artist"`
    Price float64 `json:"price"`
}
```

## Endpoints

### Return all items

When the client makes a request at **GET** /albums, we return all the items as JSON.

```go
func getAlbums(c *gin.Context) {
    c.IndentedJSON(http.StatusOK, albums)
}
```

What we have here:
- Notice that the `getAlbums` function takes an `gin.Context` parameter, this is the most important part of Gin, it carries request details, validates and serializes JSON and more.
- Call `gin.Context.IndentedJSON` to serialize the struct into JSON and add it to the response.

> [!NOTE]
> The CURl command to get the data from this endpoint is `curl -X GET localhost:8080/albums`

### Create an item

When the client makes a request at **POST** /albums it should append the Album data to the list.

```go
func postAlbum(c *gin.Context) {
    var newAlbum Album

    if err := c.BindJSON(&newAlbum); err != nil {
        return
    }

    albums = append(albums, newAlbum)
    c.Indented(http.StatusCreated, newAlbum)
}
```

What we have here:
- Use `gin.Context.BindJSON` to bind the request body to `newAlbum` variable.
- Add a 201 status code to the response along with jSON representing the album data added.

### Return an item by ID

When the client makes a request at **GET** /albums/[id], you want to return the item whose ID matches the id path parameter.

```go
func getAlbumByID(c *gin.Context) {
    id := c.Param("id")

    // Loop over the list of albums, looking for
    // an album whose ID value matches the parameter.
    for _, a := range albums {
        if a.ID == id {
            c.IndentedJSON(http.StatusOK, a)
            return
        }
    }
    c.IndentedJSON(http.StatusNotFound, gin.H{"message": "album not found"})
}
```

What we have here:
- Use `gin.Context.Param` to retrieve the `id` path parameter from the URL. When you map this handler to a path, you will include a placeholder for the parameter in the path 