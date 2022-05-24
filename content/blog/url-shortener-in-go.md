+++
title = "url shortener in go"
author = "dominic hamon"
date = "2014-01-19"
categories = [
  "go"
]
tags = [
  "go"
]
+++

# URL shortener in go

I recently combined all my various blogs into this one and then realised that,
yet again, the length of my name is a pain. Especially when it comes to URLs.
I also realised that I’d never tried to build a URL shortener, so I did.
[Full source available here](http://github.com/dominichamon/goshorturl "goshorturl @ github").

There are a few tutorials around about writing one in PHP, but I decided to use
[Go](http://golang.org/). The basic idea is the same.

## Database setup

The URL shortener uses a single table with the following structure:

Field | Type | Null | Default | Extra
------|------|------|---------|------
id    | int(11) | No | None | AUTO\_INCREMENT
short | varchar(8) | No | None | 
long  | varchar(64) | No | None |
created  | timestamp | No | CURRENT\_TIMESTAMP |
accessed | timestamp | No | 0000-00-00 00:00:00 | 
access\_count | int(11) | No | 0 | 

I think the fields are self-explanatory.

## Code

The code itself is really simple. The idea is to handle every HTTP request and
capture the path of the URI. Look the path up in the database, and return either
a redirect or not found. I decided to use
[go-sql-driver](http://github.com/go-sql-driver/mysql "go-sql-driver @ github")
for the mysql integration and [gorilla
mux](http://github.com/gorilla/mux "gorilla mux @ github") for more advanced
request routing.

```go
    func main() {
            flag.Parse()

            c, err := sql.Open("mysql", *user+":"+*pwd+"@("+*host+")/"+*db)
            if err != nil {
                    log.Fatal("Failed to open sql dbConnection")
            }
            dbConn = c
            defer dbConn.Close()

            r := mux.NewRouter()
            r.HandleFunc("/{shorturl}", Handler).Methods("GET")
            http.Handle("/", r)

            log.Fatal(http.ListenAndServe(":"+*port, nil))
    }
```

As you can see, all we do in main is open the connection to the database given
the passed-in connection parameters, then set up a simple request routing table.

```go
    func Handler(w http.ResponseWriter, r *http.Request) {
            shorturl := mux.Vars(r)["shorturl"]                                                                                                               

            // lookup shorturl
            var longurl string
            var access_count int
            err := dbConn.QueryRow("select `long`,`access_count` from `url` where short=?", shorturl).Scan(&longurl, &access_count)

            switch {
            case err == sql.ErrNoRows:
                    log.Printf("%q not found", shorturl)
                    http.NotFound(w, r)

            case err != nil:
                    log.Fatal(err)

            default:
                    access_count++
                    log.Printf("%q -> %q", shorturl, longurl)
                    http.Redirect(w, r, longurl, http.StatusFound)
            }
    }
```

The single handler is almost as straightforward. It queries the database to see
if the short URL is registered and then either returns a redirect or not found,
or fatals in the case of a SQL error. An extra feature that I might use later in
a dashboard view is that it tracks the access count and last accessed time for
each registered short URL.

We also don’t need to worry about little [Bobby Tables](http://xkcd.com/327/) as
Go escapes the parameters for us.

## Running

One final wrinkle is that I use [Dreamhost](http://dreamhost.com/) for hosting
and so can’t run a native Go server. However, with a little mod\_rewrite
trickery I can persuade Apache to forward requests to my server. Here’s the
relevant lines of the .htaccess file:

```
    RewriteEngine on 
    RewriteCond %{SERVER_PORT} !^4242$ 
    RewriteRule ^(.*) http://%{SERVER_NAME}:4242%{REQUEST_URI} [R=301,L]
```

This ensures that the given port isn’t already the one our server is listening
on, and then redirects to the host with the write port number.

## Next steps

Currently, short URLs have to be added manually through a SQL interface.
Ideally, there’d be some UI for adding short URLs, and maybe even generating
them through some hash function. Though checking for and handling hash
collisions in a database is a pain.

