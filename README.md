# clojure.dotnet

Idiomatic Clojure wrapper for ASP.NET Minimal API. Full async, auto JSON, zero ceremony.

## Install

```xml
<ClojureGitDep Include="io.github.Dangercoder/clojure.dotnet" Tag="v0.0.1" Sha="..." />
```

Or in deps.edn:

```edn
{:deps {io.github.Dangercoder/clojure.dotnet {:git/tag "v0.0.1" :git/sha "..."}}}
```

## Web

```clojure
(ns app.server
  (:require [clojure.dotnet.web :as web]))

;; String → 200 text/plain
(defn index [ctx] "Hello from ClojureCLR!")

;; Map/vector → 200 application/json (auto-serialized)
(defn status [ctx] {:status "ok" :version "1.0"})

;; Map with :status → explicit status code
(defn not-here [ctx] (web/not-found "nope"))

;; Async handler — just add ^:async
(defn ^:async fetch-data [ctx]
  (t/await (.GetStringAsync client "https://api.example.com")))

(defn -main [& args]
  (-> (web/app)
      (web/get "/" index)
      (web/get "/status" status)
      (web/get "/data" fetch-data)
      (web/start!)))
```

## Database

Async ADO.NET wrapper. Works with any provider (SQLite, Postgres, SQL Server).

```clojure
(ns app.db
  (:require [clojure.dotnet.db :as db]
            [clojure.async.task :as t])
  (:import [Microsoft.Data.Sqlite SqliteConnection]))

;; Query
(db/with-transaction [conn (db/create-connection SqliteConnection "Data Source=app.db")]
  (t/await (db/execute! conn "CREATE TABLE todos (id INTEGER PRIMARY KEY, title TEXT)"))
  (t/await (db/execute! conn "INSERT INTO todos (title) VALUES ('hello')"))
  (t/await (db/query! conn "SELECT * FROM todos")))
;; => [{"id" 1, "title" "hello"}]
```

## Requirements

- [Clojure.MSBuild](https://github.com/Dangercoder/Clojure.MSBuild) v0.0.4+
- Clojure CLR 1.12.3-alpha5+
- .NET 11.0 SDK (preview)

## License

MIT
