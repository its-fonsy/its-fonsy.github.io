# Mkdocs

Installed with

    sudo pacman -Syy mkdocs-material

then created a test directory in `\tmp/mkdocs-test` and run inside it

    mkdocs new .

then changed `mkdocs.yml` to

    site_name: Test site
    site_url: https://mydomain.org/mysite
    theme:
      name: material

then can start the server

    mkdocs serve --livereload

and check the output at "http://127.0.0.1:8000/mysite/".
