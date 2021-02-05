## togo

生成golang 代码，支持ddl、http（web，支持gzip压缩）、sql、template、i18n、httptest

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
	r.GET("/", gin.WrapH(ui.NewHandler()))
	r.GET("/app.js", gin.WrapH(ui.NewHandler()))
    r.GET("/app.css", gin.WrapH(ui.NewHandler()))
    //some code...
}

```

更多参考 `samples/`

