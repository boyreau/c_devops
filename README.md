# C DevOps infrastructure
> A C/C++ project template, a CI workflow and a monitoring stack, ready-to-use.

This project is made of 3 core products :
 - A C/C++ project template, with automated building, testing, coverage, and an action that builds, tests and store coverage informations.
 - A self-hosted gitea and an action runner.
 - An ELK stack, ready to process and display your projects and coverage stats.

![](header.png)


## Setup

This project requires:
 - docker 27.3.1 (docker compose should be built-in)

The C/C++ project templates requires:
 - clang 19.1.6
 - llvm-cov 19.1.6
 - GNU Make 4.4.1
 - A solid text editor

Feel free to switch from clang and llvm-cov to gcc and lcov.

Optionnal but useful to process stats client-side:
 - jq-1.7.1

## Installation

You need docker and docker compose.
An empty environment file named *.env.empty* can be found at the root of the project.
Rename it .env, fill the empty fields (mostly tokens and passwords) and you're ready to go.


```sh
docker compose up -d
```

Go to [your gitea](http://localhost:3000) and finish the gitea setup if required. You can also take the **gitea/config/app.ini.template** to use my configuration (don't forget to add the various tokens and secrets missing here and there).

Go to [your kibana interface](http://localhost:5601), log in with the account elastic and the password you defined in the env.
You can then click the burger menu on the top left, scroll down to stack management, click the *saved objects* menu entry in the **kibana** category and import the dashboards located in *kibana_objects/import_me.ndjson*.


## Usage example

In gitea, create a new project from the C template.
Write some code under **src/file.c** and its corresponding tests in **test/file_test.c**.

You can then run the following command to build the project:
```
make
```

You can run your test suite using:
```
make check
```

To get a coverage summary of your code, use the following (Report COVerage):
```
make rcov
```

To get a visual representation of covered/uncovered lines, you can use (Visual COVerage):
```
make vcov
```

To get raw json coverage stats :
```
make stats
```


Every push made to the built-in gitea server will run a shipped-in action that automatically generate JSON stats, clean and format them using `jq` and store them in a volume that is fed to filebeat.
A few seconds after pushing, the new stats will be visible in Kibana, in the dashboard **Average coverage**.


## Adding support for other languages

Feel free to add templates for other languages.

You should edit the action found in .gitea/workflows/coverage.yaml and configure it to build, test and get coverage about your code.
The JSON-formatted coverage stats should be put in /mnt/coverage on the action volume.

Here is the JSON format expected by the ELK stats :

```
{
    [
        {
            "filename": "/path/to/project/src/file.c" // the 'project' part of the path is processed by kibana to produce some per-project stats.
            "summary": {
                "lines": 
                {
                    "count": 0,
                    "covered": 0,
                    "percent": 100
                },
                "branches":{...},
                "regions":{...},
                ...
            }
        },
        ...
    ]
}
```

Only `filename` and `summary.lines.{count,covered,percent}` are used by the dashboard at the moment.


## Release History

* 0.0.1
    * A first release, with a gitea server, its action runner, a barely stable ELK stack and some stats.

## Meta

Zozo – Hire me –> bnzlvosnb@mozmail.com

See ``LICENSE`` for informations about the... License ! (Yeah it took me some time to guess, too)

<!-- [https://github.com/yourname/github-link](https://github.com/zozozoeee/) -->

## Contributing

1. Fork it (<https://github.com/yourname/yourproject/fork>)
2. Create your feature branch (`git checkout -b feature/fooBar`)
3. Commit your changes (`git commit -am 'Add some fooBar'`)
4. Push to the branch (`git push origin feature/fooBar`)
5. Create a new Pull Request
