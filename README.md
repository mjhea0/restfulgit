RestfulGit: A Restful API for Git data
=======================================

Provides a read-only restful interface for accessing data from Git repositories (local to the server).
Modeled off the GitHub Git DB API for compatibility (see http://developer.github.com/v3/git/).

Requires: flask, pygit2 (>= 0.18.1), libgit2 (>= 0.18).
Must modify: config.conf : repo_base_path (root path for repositories, note only repositories immediately under this path are currently supported).

gitapi.py is a valid WSGI application.

While the app can be run with "python gitapi.py" -- this runs Flask in debug mode and should NOT be used in production.
Instead the app can be run with any WSGI server, such as gunicorn (pip install gunicorn; gunicorn gitapi)

--

All of these routes return JSON unless otherwise specified.

Commits
----------
Retrieves a list of commit objects:

    GET /repos/:repo_key/git/commits
    
    optional: ?start_sha=:sha
    optional: ?ref_name=:ref_name
    optional: ?limit=:limit (default=50)

```json
[
    {
        "committer": {
            "date": "2013-05-20T16:11:30-07:00",
            "name": "Rajiv Makhijani",
            "email": "rajiv@hulu.com"
        },
        "author": {
            "date": "2013-05-20T16:11:30-07:00",
            "name": "Rajiv Makhijani",
            "email": "rajiv@hulu.com"
        },
        "url": "http://localhost:5000/repos/restfulgit.git/git/commits/f85df530d8413b0390364b291eb97d1cc5798dee",
        "tree": {
            "url": "http://localhost:5000/repos/restfulgit.git/git/trees/4c392547aa3d644877f3b22e198a5caac99a69a3",
            "sha": "4c392547aa3d644877f3b22e198a5caac99a69a3"
        },
        "sha": "f85df530d8413b0390364b291eb97d1cc5798dee",
        "parents": [
            {
                "url": "http://localhost:5000/repos/restfulgit.git/git/commits/7b3f40ff9aba370a59732522420201b744297317",
                "sha": "7b3f40ff9aba370a59732522420201b744297317"
            }
        ],
        "message": "Renamed main api file, added production recommendation to README"
    },
    ...
]
```

Retrieves specific commit object:

    GET /repos/:repo_key/git/commits/:sha

Blobs
----------
Retrieves a specific blob object:

    GET /repos/:repo_key/git/blobs/:sha

```json
{
    "url": "http://localhost:5000/repos/restfulgit.git/git/blobs/0d20b6487c61e7d1bde93acf4a14b7a89083a16d",
    "sha": "0d20b6487c61e7d1bde93acf4a14b7a89083a16d",
    "encoding": "utf-8",
    "data": "*.pyc ",
    "size": 6
}
```

Trees
----------
Retrieves a specific tree object:

    GET /repos/:repo_key/git/trees/:sha

```json
{
    "url": "http://localhost:5000/repos/restfulgit.git/git/trees/4c392547aa3d644877f3b22e198a5caac99a69a3",
    "sha": "4c392547aa3d644877f3b22e198a5caac99a69a3",
    "tree": [
        {
            "url": "http://localhost:5000/repos/restfulgit.git/git/blobs/0d20b6487c61e7d1bde93acf4a14b7a89083a16d",
            "sha": "0d20b6487c61e7d1bde93acf4a14b7a89083a16d",
            "mode": "0100644",
            "path": ".gitignore",
            "type": "blob",
            "size": 6
        },
        ...
    ]
}
```

Refs
----------
Retrieves a list of refs:

    GET /repos/:repo_key/git/refs

```json
[
    {
        "url": "http://localhost:5000/repos/restfulgit.git/git/refs/heads/master",
        "object": {
            "url": "http://localhost:5000/repos/restfulgit.git/git/commits/f85df530d8413b0390364b291eb97d1cc5798dee",
            "sha": "f85df530d8413b0390364b291eb97d1cc5798dee",
            "type": "commit"
        },
        "ref": "refs/heads/master"
    }
    ...
]
```

Retrieves a specific ref:

    GET /repos/:repo_key/git/refs/:ref_name

Raw Files
----------
Returns the raw file data for the file on the specified branch:

    /repos/:repo_key/raw/:branch_name/:file_path
