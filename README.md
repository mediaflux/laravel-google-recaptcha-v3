<!--
  Title: Laravel Recaptcha v3
  Description: Laravel recaptcha v3
  Author: ryandeng
  -->
  <meta name='keywords' content='recaptcha v3,laravel recaptcha v3,laravel google recaptcha v3,google recaptcha v3,laravel recaptcha,laravel recaptcha v3'>

# Laravel Package for Google reCAPTCHA V3

[![Latest Version on Packagist][ico-version]][link-packagist]
[![Total Downloads][ico-downloads]][link-downloads]
[![Coverage Status][ico-coverage]][link-coverage]
[![Build][ico-build]][link-build]
[![StyleCI][ico-styleci]][link-styleci]

I guarantee that bugs, issues, features and changes will be responded in 48 hours.

If you want to use v2, please go to: https://github.com/RyanDaDeng/laravel-google-recaptcha-v2

I will be super happy if you think this package is good and also star me.  ^.^

PS: For Vue component, it will be polished in the near future, however, the basic version works fine, feel free to create ticket if you encounter any issues.

# Table of Contents
0. [Configurations](#settings)
1. [Facade Usage](#facade-usage)
2. [Blade Usage](#blade-basic-usage)
3. [Vue Usage](#vue-usage)
4. [Validation](#validation-class)
5. [Advanced Usage](#advanced-usage)
6. [Contributors](#contributors)

# DEMO

## Invisible - hidden

<img src="https://github.com/RyanDaDeng/aws-study-notes/blob/master/881545398213_.pic.jpg" width="250" height="300" />

## Inline

<img src="https://github.com/RyanDaDeng/aws-study-notes/blob/master/891545398531_.pic.jpg" width="250" height="300" />

## Corner

<img src="https://github.com/RyanDaDeng/aws-study-notes/blob/master/901545398593_.pic.jpg" width="150" height="100" />

## Description

Google reCAPTCHA v3 is a new mechanism to verify whether the user is bot or not.

reCAPTCHA v3 is intended for power users, site owners that want more data about their traffic, and for use cases in which it is not appropriate to show a challenge to the user.

For example, a registration page might still use reCAPTCHA v2 for a higher-friction challenge, whereas more common actions like sign-in, searches, comments, or voting might use reCAPTCHA v3.

Please check Google site: https://developers.google.com/recaptcha/docs/faq

## Features

- Score Comparision
- Support invisible, global and inline badge style
- Support multiple reCAPTCHA on the same page for different forms
- Support multiple actions to be placed on the same page
- Support custom implementation on config interface
- Support custom implementation on request method interface 


## Requirement

This package requires the following dependencies:

- Laravel 5.x 

- If you want to use Validation Class your Laravel version needs to be >= 5.5

- php > 5

- Please ensure that you have read basic information from Google reCAPTCHA v3.

## Installation


Via Composer

``` sh
        $ composer require timehunter/laravel-google-recaptcha-v3 "~2.0.0" -vvv
```

If your Laravel framework version <= 5.4, please register the service provider under your config file: /config/app.php, otherwise please skip it.


``` php
'providers'=[
    ....,
    TimeHunter\LaravelGoogleReCaptchaV3\Providers\GoogleReCaptchaV3ServiceProvider::class
]
```

And also
``` php
'aliases'=[
     ....,
     'GoogleReCaptchaV3'=> TimeHunter\LaravelGoogleReCaptchaV3\Facades\GoogleReCaptchaV3::class
 ]
```


If your Laravel framework version is >= 5.5, just run the following command to publish config.
```sh 
$ php artisan vendor:publish --provider="TimeHunter\LaravelGoogleReCaptchaV3\Providers\GoogleReCaptchaV3ServiceProvider" --tag=googlerecaptchav3.config
```

For vue component:
```sh 
$ php artisan vendor:publish --provider="TimeHunter\LaravelGoogleReCaptchaV3\Providers\GoogleReCaptchaV3ServiceProvider" --tag=googlerecaptchav3.vuejs
```

After installation, you should see a googlerecaptchav3.php in your app/config folder, and vue component under js/components/googlerecaptchav3 folder.

## Configurations <a name="settings" />
### Setting up your Google reCAPTCHA details in config file

Please register all details in config for host_name, site_key, secret_key and site_verify_url.

Specify your Score threshold and action in 'setting', e.g.
``` php
      'setting' =  [
          [
            'action' => 'contact_us', // Google reCAPTCHA required paramater
            'threshold' => 0.2, // score threshold
            'is_enabled' => false // if this is true, the system will do score comparsion against your threshold for the action
            ]
        ]
```        
Note: if you want to enable Score Comparision, you also need to enable is_score_enabled to be true.
``` php
'is_score_enabled' = true
```   

For score comparision, all actions should be registered in googlerecaptchav3 config file under 'setting' section. 

For more details please check comments in config file.



## Facade Usage <a name="facade-usage" />

You can directly use registered facade service by calling the following methods.

- setAction() is optional only if you want to verify if the action is matched.
- verifyResponse() which accepts the token value from your form. This returns Google reCAPTCHA Response object.
- setScore() is optional only if you want to manually verify the score.

``` php
   GoogleReCaptchaV3::setAction($action)->verifyResponse($value,$ip = null);
```

Example Usage

``` php
   GoogleReCaptchaV3::verifyResponse($value,$ip)->getMessage();
   GoogleReCaptchaV3::verifyResponse($value)->isSuccess();
   GoogleReCaptchaV3::verifyResponse($value)->toArray();
```

 
``` php
   GoogleReCaptchaV3::verifyResponse(
                         $request->input('g-recaptcha-response'), 
                         $request->getClientIp()
                         )
                      ->getMessage()
```


``` php
   GoogleReCaptchaV3::setAction($action)->verifyResponse($value)->isSuccess();
 ```
 
If you manually assign a value to setScore($score), the code will fully skip your config file and force to check the score.
 
``` php
   GoogleReCaptchaV3::setScore($score)
                    ->setAction($action)
                    ->verifyResponse(
                        $request->input('g-recaptcha-response'), 
                        $request->getClientIp()
                        )
                    ->getMessage()
```


## Validation Class (Only support Laravel >= 5.5) <a name="validation-class" />
   
   You can use provided Validation object to verify your reCAPTCHA.
      
``` php
   use TimeHunter\LaravelGoogleReCaptchaV3\Validations\GoogleReCaptchaV3ValidationRule;
   $rule = [
            'g-recaptcha-response' => [new GoogleReCaptchaV3ValidationRule('action_name')]
        ];
```

   -  $actionName: if its NULL, the package won't verify action with google response.
  


## Blade Usage <a name="blade-basic-usage" />
### Display reCAPTCHA v3 

#### Blade
Include div with an ID inside your form, e.g.

``` html  
 <div id="contact_us_id"></div>
```

Include Template script in your bottom/header of your page, params should follow 'ID'=>'Action', e.g.

``` PHP  
 {!!  GoogleReCaptchaV3::render(
            [
                'contact_us_id'=>'contact_us',  // the div id=contact_us_id maps to action name contact_us
                'signin_id'=>'registration',  // the div id=signin_id maps to action name registration
                'register_id'=>'registration'   // the div id=register_id maps to action name registration
            ]) !!}
```

#### Example Usage

``` html  

{{--if laravel version <=5.6, please use {{ csrf_field() }}--}}

<form method="POST" action="/verify">
    @csrf
    <div id="contact_us_id"></div>
    <input type="submit" value="submit">
    <div>
        <small>
            This site is protected by reCAPTCHA and the Google
            <a href="https://policies.google.com/privacy">Privacy Policy</a> and
            <a href="https://policies.google.com/terms">Terms of Service</a> apply.
        </small>
    </div>
</form>

{!!  GoogleReCaptchaV3::render([
               'contact_us_id'=>'contact_us'
           ]) !!}

```

The backend request will receive a value for 'g-recaptcha-response', please take a look at Sample Use Case and Facade usage sections.

### Badge Display

If your settings were not reflected, please run php artisan config:cache to clear cache.

Inline

1. Go to config file, and set 
``` PHP
    [
        ...
        'inline' => true
        ...
    ]
```
2. Badge will be displayed as inline format within the form.


Invisible

1. Set inline as true as well
2. Modify your div with style display:none
3. Refer to Google official site: https://developers.google.com/recaptcha/docs/faq
, you need to include the following text:
 ``` HTML
    This site is protected by reCAPTCHA and the Google
        <a href="https://policies.google.com/privacy">Privacy Policy</a> and
        <a href="https://policies.google.com/terms">Terms of Service</a> apply.
 ```

Corner

1. Set inline as false
2. Your badge will be shown in the bottom right side.

Custom

1. Set inline as true
2. Do Styling/CSS on its div element


### Blade Use Case

Register your action in config, also enable score and set up your own site key and secret key:
``` php
    'setting' => [
        [
            'action' => 'contact_us',
            'threshold' => 2,
            'score_comparision' => true
        ],
        [
            'action' => 'signup',
            'threshold' => 0.2,
            'score_comparision' => true
        ],
    ]
```

Register two routes in web.php
``` php
Route::get('/index', 'ReCaptchaController@index');
Route::post('/verify', 'ReCaptchaController@verify');
```

Create two functions in controller:
``` php
    public function verify(Request $request)
    {
        dd(GoogleReCaptchaV3::verifyResponse($request->input('g-recaptcha-response'))->getMessage());
    }
    public function index(Request $request)
    {
        return view('index');    
   }
```

Create your form in index.blade.php:
``` html
{{--if laravel version <=5.6, please use {{ csrf_field() }}--}}

<form method="POST" action="/verify">
    @csrf
    <div id="contact_us_id"></div>
    <input type="submit" value="submit">
</form>


<form method="POST" action="/verify">
    @csrf
    <div id="signup_id"></div>
    <input type="submit" value="submit">
</form>

{!!  GoogleReCaptchaV3::render(['contact_us_id'=>'contact_us', 'signup_id'=>'signup']) !!}
```

Go to /index and click submit button on contact us form and you should see an error message that 'Score does not meet the treshhold' because the threshold >2. You can play around the controller to see all outcomes. Importantly, you need to wait the script to be loaded before clicking the submit button.

## Vue Usage <a name="vue-usage" />

The package provides a lightweight vue component. You need to publish the vue component before playing around it.

### Step 1 Publish vue component:
```sh 
$ php artisan vendor:publish --provider="TimeHunter\LaravelGoogleReCaptchaV3\Providers\GoogleReCaptchaV3ServiceProvider" --tag=googlerecaptchav3.vuejs
```

The file will be created under js/components/googlerecaptchav3/GoogleReCaptchaV3.vue, you have full control and modification ability on this file.


### Step 2 Import vue component and Register reCAPTCHA v3 SiteKey

The Blade way is no longer useful if you use Vue, so we need to manage to assign site key by ourselves. The component supports props below:

Supported: siteKey, elementId, inline and action, check the original file to see the details.

````vue
<google-re-captcha-v3
                :siteKey="this.siteKey"
                :elementId="'contact_us_id'"
                :inline="true"
                :action="'contact_us'">
</google-re-captcha-v3>
        
```` 


There are two ways you can bind site key to the component.

### Use prop

````vue
<template>
    <div>
        <form method="POST" action="/verify">
            <div>
                <label for="exampleInputEmail1">Email address</label>
                <input type="email"  id="exampleInputEmail1"
                       aria-describedby="emailHelp"
                       placeholder="Enter email">
            </div>
            <div>
                <label for="exampleInputPassword1">Password</label>
                <input type="password" id="exampleInputPassword1"
                       placeholder="Password">
            </div>
            <div>
                <div id="contact_us_id"></div> // Your reCAPTCHA div element
            </div>

            <button type="submit">Submit</button>
        </form>
        <google-re-captcha-v3
                :siteKey="this.siteKey"
                :elementId="'contact_us_id'"
                :inline="true"
                :action="'contact_us'">

        </google-re-captcha-v3>
    </div>

</template>
<script>
    import GoogleReCaptchaV3 from '../components/googlerecaptchav3/GoogleReCaptchaV3'; // location might be diff to your case, ensure that your location is right

    export default {
        components: {
            GoogleReCaptchaV3
        },
        data() {
            return {
                siteKey: 'YOUR_SITE_KEY', // provide your site key here
            }
        },
        created() {

        },
        computed: {},
        mounted() {

        },
        watch: {},
        methods: {}
    }
</script>

````

###  or Add site key directly into GoogleReCaptchaV3 component

Alternatively, I believe most of cases your site key will never be changed, so you could just modify the original published component to have sitekey hard-coded in.

For instance, open published file and find code below:
````vue
        ....
        props: {
                siteKey: {
                    type: String,
                    required: true
                },
               .....
            },
       ....

````

Remove it from prop and add it in data():

````vue
 data() {
            return {
                siteKey: "YOUR_KEY_HERE",
                ....
            }
        },
````     


## Advanced Usage <a name="advanced-usage" />

### Custom implementation on Config
    
For some users, they might store the config details in their own storage e.g database. You can create your own class and implement:

```
TimeHunter\LaravelGoogleReCaptchaV3\Interfaces\ReCaptchaConfigV3Interface
```

Remember to register it in your own service provider

``` php
     $this->app->bind(
                ReCaptchaConfigV3Interface::class,
                YourOwnCustomImplementation::class
            );
```

### Custom implementation on Request method

The package has two default options to verify: Guzzle and Curl, if you want to use your own request method, You can create your own class and implement 
```
TimeHunter\LaravelGoogleReCaptchaV3\Interfaces\RequestClientInterface
```

Remember to register it in your own service provider
``` php
     $this->app->bind(
                RequestClientInterface::class,
                YourOwnCustomImplementation::class
            );
```


## Contributors  <a name="contributors" />

Thank you for the following contributors, You guys are the BEST!

* [@xalunda](https://github.com/xalunda)

## Security

If you discover any security related issues, please email ryandadeng@gmail.com instead of using the issue tracker.


## License

MIT. Please see the [license file](license.md) for more information.

[ico-version]: https://poser.pugx.org/timehunter/laravel-google-recaptcha-v3/version
[ico-downloads]: https://poser.pugx.org/timehunter/laravel-google-recaptcha-v3/downloads
[ico-coverage]: https://coveralls.io/repos/github/RyanDaDeng/laravel-google-recaptcha-v3/badge.svg?branch=master&service=github
[ico-build]: https://travis-ci.org/RyanDaDeng/laravel-google-recaptcha-v3.svg?branch=master
[ico-styleci]: https://github.styleci.io/repos/146857583/shield
[ico-unstable]: https://poser.pugx.org/timehunter/laravel-google-recaptcha-v3/v/unstable

[link-packagist]: https://packagist.org/packages/timehunter/laravel-google-recaptcha-v3
[link-downloads]: https://packagist.org/packages/timehunter/laravel-google-recaptcha-v3
[link-author]: https://github.com/ryandadeng
[link-coverage]: https://coveralls.io/github/RyanDaDeng/laravel-google-recaptcha-v3?branch=master
[link-build]: https://travis-ci.org/RyanDaDeng/laravel-google-recaptcha-v3
[link-styleci]: https://github.styleci.io/repos/146857583
[link-unstable]: https://packagist.org/packages/timehunter/laravel-google-recaptcha-v3
