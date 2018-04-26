# Supported tags and respective `Dockerfile` links

- `standard`
- `multi-instance`

# What is this image?

This image allows running a PHP application. All PHP extensions required by
silverorange are included in the image, as well as tools like `composer`,
`npm`, `yarn`, and `git`.

# How to use this image.

## Installation / Usage

Install the `silverorange/php-app` container:
``` sh
$ docker pull silverorange/php-app
```

Or alternatively, pull a specific version of `silverorange/php-app`:
``` sh
$ docker pull composer/composer:standard
```

# Image Variants

## `silverorange/php-app:standard`

Use this image for running standard, single-instance web applications.

## `silverorange/php-app:multi-instance`

Use this image for running multi-instance web applications.