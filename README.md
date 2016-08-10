# apig: Golang RESTful API Server Generator
[![Build Status](https://travis-ci.org/wantedly/apig.svg?branch=master)](https://travis-ci.org/wantedly/apig)

apig is an RESTful API server generator.

* Input: Model definitions based on [gorm](https://github.com/jinzhu/gorm) annotated struct
* Output: RESTful JSON API server using [gin](https://github.com/gin-gonic/gin) including tests and documents

## How to build and install

Prepare Go 1.6 or higher.
Go 1.5 is acceptable, but `GO15VENDOREXPERIMENT=1` must be set.

After installing required version of Go, you can build and install `apig` by

```bash
$ go get -d -u github.com/wantedly/apig
$ cd $GOPATH/src/github.com/wantedly/apig
$ make
$ make install
```

`make` generates binary into `bin/apig`.
`make install` put it to `$GOPATH/bin`.

## How to use

### 1. Generate boilerplate

First, creating by `apig new` command.

```
$ apig new -u wantedly apig-sample
```

generates Golang API server boilerplate under `$GOPATH/src/gihhub.com/wantedly/apig-sample`.
You can omit `-u, --user` and `--vcs` options. The default of `-u, -user` is github username, and `--vcs` is `github.com`

### 2. Write model code

Second, write model definitions under models/. For example, user and email model is like below:

```
// models/user.go
package models

import "time"

type User struct {
	ID        uint       `gorm:"primary_key;AUTO_INCREMENT" json:"id"`
	Name      string     `json:"name"`
	Emails    []Email    `json:"emails"`
	CreatedAt *time.Time `json:"created_at"`
	UpdatedAt *time.Time `json:"updated_at"`
}
```

```
// models/email.go
package models

type Email struct {
	ID      uint   `gorm:"primary_key;AUTO_INCREMENT" json:"id"`
	UserID  uint   `json:"user_id"`
	Address string `json:"address"`
	User    *User  `json:"user`
}
```

This models are based on [gorm](https://github.com/jinzhu/gorm) structure.
Please refer [gorm document](http://jinzhu.me/gorm/) to write detailed models.

### 3. Generate controllers, tests, documents etc. based on models.

Third, run the command:

```
apig gen
```

It creates all necessary codes to provide RESTful endpoints of models.

### 4. Build and run server

Finally, just build as normal go code.

```bash
$ go get ./...
$ go build -o bin/server
```

After that just execute the server binary.
For the first time, you may want to use `AUTOMIGRATE=1` when running the server.

```
$ AUTOMIGRATE=1 bin/server
```

When `AUTOMIGRATE=1`, the db tables are generated automatically.
After that, you can run the server just executing the command:

```
$ bin/server
```

The server runs at http://localhost:8080


## Usage

### `new` command
`new` command tells apig to generate API server skeleton.

```
$ apig new NAME
```

### `gen` command
`gen` command tells apig to generate files (routes, controllers, documents...) from [gorm](https://github.com/jinzhu/gorm) model files you wrote.

You MUST run this command at the directory which was generated by `new` command.

```
$ apig gen
```

### API Document

API Documents are generated automatically in `docs/` directory in the form of [API Blueprint](https://apiblueprint.org/).

```
docs
├── email.apib
├── index.apib
└── user.apib
```

[Aglio](https://github.com/danielgtaylor/aglio) is an API Blueprint renderer.
Aglio can be installed by

```
$ npm install -g aglio
```

You can generate HTML files and run live preview server.

```
// html file
$ aglio -i index.apib  -o index.html

// running server on localhost:3000
$ aglio -i index.apib --server
```

`index.apib` includes other files in your blueprint.

## License
[![MIT License](http://img.shields.io/badge/license-MIT-blue.svg?style=flat)](LICENSE)
