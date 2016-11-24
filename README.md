wp-cli-fixtures
=========================

[![Build Status](https://travis-ci.org/nlemoine/wp-cli-fixtures.svg?branch=master)](https://travis-ci.org/nlemoine/wp-cli-fixtures)

Inspired by [Faker](https://github.com/trendwerk/faker), this package provides an easy way to create massive and custom fake data for your WordPress installation.
This package is based on [nelmio/alice](https://github.com/nelmio/alice) and [fzaninotto/Faker](https://github.com/fzaninotto/Faker). Please refer to these packages docs for advanced usage.

**DISCLAIMER:** This package is mostly intented to be used for development purposes. Don't use it on a production database or be sure to back it up first.

Quick links: [Install](#install) | [Usage](#usage) | [Contributing](#contributing)

## Install

```
wp package install hellonico/wp-cli-fixtures
```
Requires [wp-cli](https://github.com/wp-cli/wp-cli) >= 0.23 and PHP >= 7.0.

## Usage

### Create fixtures

At the root of your project, create a `fixtures.yml` file:

```yaml
Hellonico\Fixtures\Entity\User:
  user{1..10}:
    user_login (unique): <username()> # (unique) is required
    user_pass: '123456'
    user_email: '<safeEmail()>'
    first_name: '<firstName()>'
    last_name: '<lastName()>'
    description: '<sentence()>'
    meta:
        phone_number: '<phoneNumber()>'
        address: '<streetAddress()>'
        zip: '<postcode()>'
        city: '<city()>'

Hellonico\Fixtures\Entity\Attachment:
  attachment{1..15}:
    post_title: '<sentence()>'
    file: <image(<uploadDir()>, 1200, 1200, 'cats')> # <uploadDir()> is required

Hellonico\Fixtures\Entity\Term:
  category{1..10}:
    name (unique): '<words(2, true)>' # (unique) is required
    description: '<sentence()>'
    taxonomy: 'category'

Hellonico\Fixtures\Entity\Post:
  post{1..30}:
    post_title: '<sentence()>'
    post_content: '<paragraphs(5, true)>'
    post_excerpt: '<paragraphs(1, true)>'
    # meta and meta_input are basically the same, you can use one or both, 
    # they will be merged, just don't provide the same keys in each definition
    meta:
        _thumbnail_id: '@attachment*->ID'
    meta_input:
        _extra_field: '<paragraphs(1, true)>'
    post_category: '3x @category*->term_id' # post_category only accepts IDs
    tax_input:
      post_tag: '5x <words(2, true)>' # Tags can be dynamically created

Hellonico\Fixtures\Entity\Comment:
  comment{1..50}:
    comment_post_ID: '@post*->ID'
    user_id: '@user*->ID'
    comment_author: '<username()>'
    comment_author_email: '<safeEmail()>'
    comment_author_url: '<url()>'
    comment_content: '<paragraphs(2, true)>'
    comment_agent: '<userAgent()>'

```

The example above will generate:

- 10 users
- 15 attachments
- 10 terms (categories)
- 30 posts with a thumbnail, 3 categories and 5 tags
- 50 comments

**IMPORTANT:** Make sure referenced IDs are placed **BEFORE** they are used.

Example: `Term` or `Attachment` objects **must** be placed before `Post` if they are referenced in your posts fixtures.

### Load fixtures

```
wp fixtures load
```

You can also specify a custom file by using the `--file` argument:

```
wp fixtures load --file=data.yml
```

### Delete fixtures

```
wp fixtures delete
```

You also can delete a single fixture type:

```
wp fixtures delete post
```

Valid types are `post`, `attachment`, `comment`, `term`, `user`.

### Custom formatters

In addition to formatters provided by [fzaninotto/Faker](https://github.com/fzaninotto/Faker), you can use custom formatters below.

#### `postId($args)`

Returns an random existing post ID. 
`$args` is optional and can list any arguments from [`get_posts`](https://developer.wordpress.org/reference/functions/get_posts/#parameters)

Example:

```
<postId(category=1,2,3)>
```

#### `attachmentId($args)`

Returns an random existing attachment ID. 
`$args` is optional and can list any arguments from [`get_posts`](https://developer.wordpress.org/reference/functions/get_posts/#parameters)

Example:

```
<attachmentId(year=2016)>
```

#### `termId($args)`

Returns an random existing term ID. 
`$args` is optional and can list any arguments from [`get_terms`](https://developer.wordpress.org/reference/functions/get_terms/#parameters)

Example:

```
<termId(taxonomy=post_tag)>
```

#### `userId($args)`

Returns an random existing user ID. 
`$args` is optional and can list any arguments from [`get_users`](https://developer.wordpress.org/reference/functions/get_users/#parameters)

Example:

```
<userId(role=subscriber)>
```


## Contributing

This package follows PSR2 coding standards. Please ensure your PR sticks to these standards.
