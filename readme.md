# golang-api

This API is based on the tutorial *Developing a RESTful API with Go and Gin*

## Endpoints

### Return all items

When the client makes a request at **GET** /albums, we return all the items as JSON.

'''go
func getAlbums(c *gin.Context) {
    c.IndentedJSON(http.StatusOK, albums)
}
'''