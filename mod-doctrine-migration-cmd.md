Install doctrine/migrations:

```bash
$ composer require "doctrine/migrations"
```

mkdir a new migration folder
In the root of application, add php configuration file, and add migration-db
file to connect to the database

To generate new migration class,

```bash
./vendor/bin/doctrine-migrations generate
```

To check status,

```bash
$ ./vendor/bin/doctrine-migrations status
```

To test and do dry run

```bash
$ ./vendor/bin/doctrine-migrations migrate --dry-run
```

To do the real, just do migrate

```bash
$ ./vendor/bin/doctrine-migrations migrate
```

To do migrations down and up

```bash
$./vendor/bin/doctrine-migrations migrations:execute --down 'MyProject\Migrations\Version20230817041640'
```

for further information, please reference project MIGRATION-DOCS-EXAMPLE
project in www folder and read readme.md
