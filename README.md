## togo

生成golang 代码，支持ddl、http（web）、sql、template、i18n、httptest

## usage

### 数据库表升级


```go

//go:generate togo ddl -package mysql -dialect mysql

```

```
go generate ./

```

数据库初始化
```go
    datasource := "root:123456@tcp(127.0.0.1:3306)/?charset=utf8"
	db, err := sql.Open("mysql", datasource)
	if err != nil {
		return err
	}
	defer db.Close()
	db.Exec("CREATE DATABASE test DEFAULT CHARACTER SET utf8 COLLATE utf8_bin")
	_, err = db.Exec("use test")
	if err != nil {
		return err
	}
	if err := mysql.Migrate(db); err != nil {
		return err
    }
    return nil


```

### Web 打包

```go
//go:generate togo http -package ui -output dist_gen.go -trim-prefix dist -input=dist/**

```

```
go generate ./

```

gin 使用
```go

func main(){
    //some code...
    uiHandle := MakeGzipHandler(http.FileServer(ui.New()))
	r.GET("/", gin.WrapH(uiHandle))
	r.GET("/app.js", gin.WrapH(uiHandle))
    r.GET("/app.css", gin.WrapH(uiHandle))
    //some code...
}


type gzipResponseWriter struct {
	io.Writer
	http.ResponseWriter
}

// Use the Writer part of gzipResponseWriter to write the output.
func (w gzipResponseWriter) Write(b []byte) (int, error) {
	return w.Writer.Write(b)
}

// MakeGzipHandler adds support for gzip compression for given handler
func MakeGzipHandler(handler http.Handler) http.Handler {
	return http.HandlerFunc(func(w http.ResponseWriter, r *http.Request) {
		// Check if the client can accept the gzip encoding.
		if !strings.Contains(r.Header.Get("Accept-Encoding"), "gzip") {
			handler.ServeHTTP(w, r)
			return
		}

		// Set the HTTP header indicating encoding.
		w.Header().Set("Content-Encoding", "gzip")
		gz := gzip.NewWriter(w)
		defer gz.Close()
		gzw := gzipResponseWriter{Writer: gz, ResponseWriter: w}
		handler.ServeHTTP(gzw, r)
	})
}

```

更多参考 `samples/`

