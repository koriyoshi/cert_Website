<pre>
     _                 _                                    _ _
 ___(_)_ __ ___  _ __ | | ___  _ __     ___ _ __ ___   __ _(_) |
/ __| | '_ ` _ \| '_ \| |/ _ \| '_ \   / _ \ '_ ` _ \ / _` | | |
\__ \ | | | | | | |_) | | (_) | | | | |  __/ | | | | | (_| | | |
|___/_|_| |_| |_| .__/|_|\___/|_| |_|  \___|_| |_| |_|\__,_|_|_|
                |_|                                             
</pre>

# Simplon Email

This library helps to build emails by utilising [SwiftMailer](https://github.com/swiftmailer/swiftmailer).

It enables the developer to send an email as plain/html version with automatic image detection/inline injection. It enforces template abstraction and offers injection of data.

### Setup

Since its a composer package all you need to to do is require it within your composer package definitions and install/update it:

```json
{
     "require": {
        "simplon/email": "*"
     }
}
```

If you dont know what ```composer``` is you should have a look at [Composer's Webpage](http://getcomposer.org/doc/00-intro.md).

### EmailTransportVo

This class holds our chosen email transport instance in order to send emails.

```php
// php's internal mail
$emailTransportVo = (new \Simplon\Email\Vo\EmailTransportVo())->setTransportInstance(
	\Swift_MailTransport::newInstance()
);

// or via smtp transport
$emailTransportVo = (new \Simplon\Email\Vo\EmailTransportVo())->setTransportInstance(
	\Swift_SmtpTransport::newInstance('localhost', 25)
		->setUsername('foo')
		->setPassword('bar')
);
```

### Email setup

#### Intro

We use builder pattern classes to define our email. It's really simple and helps us to stay in control of our data.

Building an email comes in two easy steps. The first step is about defining our email content with the help of templates. The second step defines the sender/recipient data as well as a subject.

#### Templates

All what is needed is to tell the class where to find our templates for this particular email. Templates are separated in two different types:

**Base templates**
- NOT required
- need to be named "base.plain" \ "base.html"

Example:

```plain
HEADER

#################################################

A base-template is the basis to all content-templates.
Content will replace the following placeholder:

{{content}}

#################################################

FOOTER

-------------------------------------------------

A base-template can read all injected
content variables, too. See below:

Today is {{date}}
```

**Content templates**
- required
- need to be named "content.plain" \ "content.html"

```plain
Oh herro!

This is a content template which can receive
content data such as the recipients name.

Be nice, {{name}}!

Thanks
Tino

PS: {{name}}'s age is {{age}}
```

Note: We enforce these names just to make it as easy and as quick as possible to setup your email.

Here is a snapshot from our test template folders which hold all data:

**Folder structure**

```bash
|-templates
|---base
|---content
|-----tmpl01
|-----tmpl02
|-----tmpl03
```

**Base templates**

```bash
drwxr-xr-x  4 fightbulc  staff  136  7 Jul 15:52 .
drwxr-xr-x  5 fightbulc  staff  170  7 Jul 15:52 ..
-rw-r--r--  1 fightbulc  staff  498  7 Jul 13:12 base.html
-rw-r--r--  1 fightbulc  staff  377  7 Jul 13:27 base.plain
```

**Content templates** (example multipart email)

```bash
drwxr-xr-x  5 fightbulc  staff     170  7 Jul 15:52 .
drwxr-xr-x  6 fightbulc  staff     204  7 Jul 15:52 ..
-rw-r--r--@ 1 fightbulc  staff  170870  6 Jul 13:54 boat.jpg
-rw-r--r--  1 fightbulc  staff     332  7 Jul 13:29 content.html
-rw-r--r--  1 fightbulc  staff     166 30 Jun 15:54 content.plain
```

#### Create email

Alright, lets create our first email. Below we will set all relevant data. Just follow the example:

```php
// set content data
$contentData = [
    'name' => 'Tino',
    'age'  => 32,
    'date' => date('r'),
];

```php
$emailVo = (new \Simplon\Email\Vo\EmailVo())
    ->setPathBaseTemplates(__DIR__ . '/templates/base') // optional
    ->setPathContentTemplates(__DIR__ . '/templates/content/tmpl01')
    ->setFrom($config['fromAddress'], $config['fromName'])
    ->setTo($config['toAddress'], $config['toName'])
    ->setSubject('Herro!')
    ->setContentData($contentData)
```

#### Send email

Almost done. Just pass our builder classes and the email is as good as gone :)

```php
$response = (new \Simplon\Email\Email($emailTransportVo))->sendEmail($emailVo);
```

### Complete example

See the following complete example of sending an email.

```php
require __DIR__ . '/../vendor/autoload.php';

// load test data
require __DIR__ . '/config.php';

// ##########################################

// set content data
$contentData = [
    'name' => 'Tino',
    'age'  => 32,
    'date' => date('r'),
];

// set email
$emailVo = (new \Simplon\Email\Vo\EmailVo())
    ->setPathBaseTemplates(__DIR__ . '/templates/base')
    ->setPathContentTemplates(__DIR__ . '/templates/content/tmpl01')
    ->setFrom($config['fromAddress'], $config['fromName'])
    ->setTo($config['toAddress'], $config['toName'])
    ->setSubject('Herro!')
    ->setContentData($contentData);

// ------------------------------------------

// set transport
$emailTransportVo = new \Simplon\Email\Vo\EmailTransportVo(
	\Swift_MailTransport::newInstance()
);

// ------------------------------------------

// send email
$response = (new \Simplon\Email\Email($emailTransportVo))->sendEmail($emailVo);

// BOOL to indicate if all went fine
var_dump($response);
```

# Anything else?
Still in doubt how to use this library? Have a look at the ```test``` folder.

# License
Simplon\Email is freely distributable under the terms of the MIT license.

Copyright (c) 2015 Tino Ehrich ([tino@bigpun.me](mailto:tino@bigpun.me))

Permission is hereby granted, free of charge, to any person obtaining a copy of this software and associated documentation files (the "Software"), to deal in the Software without restriction, including without limitation the rights to use, copy, modify, merge, publish, distribute, sublicense, and/or sell copies of the Software, and to permit persons to whom the Software is furnished to do so, subject to the following conditions:

The above copyright notice and this permission notice shall be included in all copies or substantial portions of the Software.

THE SOFTWARE IS PROVIDED "AS IS", WITHOUT WARRANTY OF ANY KIND, EXPRESS OR IMPLIED, INCLUDING BUT NOT LIMITED TO THE WARRANTIES OF MERCHANTABILITY, FITNESS FOR A PARTICULAR PURPOSE AND NONINFRINGEMENT. IN NO EVENT SHALL THE AUTHORS OR COPYRIGHT HOLDERS BE LIABLE FOR ANY CLAIM, DAMAGES OR OTHER LIABILITY, WHETHER IN AN ACTION OF CONTRACT, TORT OR OTHERWISE, ARISING FROM, OUT OF OR IN CONNECTION WITH THE SOFTWARE OR THE USE OR OTHER DEALINGS IN THE SOFTWARE.


[![Bitdeli Badge](https://d2weczhvl823v0.cloudfront.net/fightbulc/simplon_email/trend.png)](https://bitdeli.com/free "Bitdeli Badge")

