
# Laravel utilities <img src="https://img.shields.io/badge/repository-public-green.svg?logo=LOGO">

A collection of utilities to Laravel projects. This package

## Requirements
- PHP ^8.0
- Laravel ^9.2

## Installation
Add this repository in your <strong>composer.json</strong>
```bash
"repositories": [  
    {  
        "type": "vcs",  
        "url": "https://github.com/labelgrupnetworks/laravel-utilities"  
    }  
]
```
After install your composer dependencies.
```bash
composer install
```
Composer will require an access token to install the dependency, write it down if you already have one.

>This package will be constantly updated. If you want to update the package to have the latest version:
```bash
composer update labelgrup/laravel-utilities
```

## Table of contents
- [Commands](#commands)
- [CustomException](#customException)
- [Helpers](#helpers)
- [Rules](#rules)

## Commands

- [MakeApiRequest](#makeApiRequest)
- [MakeUseCase](#makeUseCase)

### MakeApiRequest
Command to create a Request adapted for API requests in <strong>/App/Http/Requests/Api/</strong>. Parse fails request and response using JSON.
>This entity works like native Laravel Requests
```bash
php artisan make:api-request {ApiRequestName}
```

### MakeUseCase
Command to create a <strong>UseCase</strong> in <strong>/App/UseCases/</strong> to decouple the different <strong>Strong</strong> actions.
```bash
php artisan make:use-case {UseCaseName} {--without-validation}
```
The class generated extend to <strong>UseCase</strong> and has <strong>UseCaseInterface</strong>. This class required implement the method <strong>action</strong> and have the <strong>handler</strong> method to response.

```php
use \Labelgrup\LaravelUtilities\Core\UseCases\UseCase;
use \Labelgrup\LaravelUtilities\Core\UseCases\WithValidateInterface;

class ExampleUseCase extends UseCases\UseCase implements WithValidateInterface
{
    /**
	* Methods inherited from UseCase
	*
	* handle(): Method to called to get UseCaseResponse
	* perform(): Method to call $this->validate() (if implemented) and if no throw exception, call & return $this->action()
	*/
	
	/**
	 * Implement your use case
	 */
	public function action()
	{
		// Implement your use case
	}

	/**
	 * Validate the use case
	 */
	public function validate(): void
	{
		// Implement your validation
	}
}
```

If you want to customize the response implement the <strong>handle</strong> and return method to <strong>UseCaseResponse</strong>.

- You can specify `$response_message` to set a message for OK response.
- You can specify `$success_status_code`  to set a status code for OK response. If not set, the default is 200.

```php
public string $response_message = 'Action has been finished';
```
## CustomException
This class provide a custom exception to Laravel projects. This class extends from <strong>Exception</strong> and implements <strong>Renderable</strong> interface. This class has a method <strong>render</strong> to parse the exception to a response.

```php
public function __construct(
    public string $error_code,
    public string $error_message,
    public int $http_code = Response::HTTP_INTERNAL_SERVER_ERROR,
    public ?array $report_data = ['logs' => []],
    public ?array $response = [],
    public bool $should_render = true
)
```
- `error_code`: The error code to identify the exception.
- `error_message`: The error message to show in the response.
- `http_code`: The HTTP code to show in the response.
- `report_data`: The data to report in the logs.
- `response`: The response to show in the response.
- `should_render`: If the exception should be rendered.

## Helpers
- [ApiResponse](#apiResponse)
- [ExceptionHandler](#exceptionHandler)
- [Image](#image)
- [Password](#password)
- [Text](#text)
- [Zip](#zip)

### ApiResponse
This class helps to parse responses for APIs. Methods:
```php
// Help to parse a success response
public static function done(
	string $message,
	array $data = [],
	int $code = Response::HTTP_OK
): JsonResponse

// Help to parse a fail response
public static function fail(
	string $message,
	array $errors = [],
	int $code = Response::HTTP_OK
): JsonResponse

// Help to parse a response
public static function response(
	array $data,
	int $code
): JsonResponse

// Help to parse a response
public static function response(
	array $data,
	int $code
): JsonResponse
```
### ExceptionHandler
This class helps to handle exceptions in Laravel projects. Methods:
```php
public static function render(\Throwable $exception, Request $request)
```

This class you can use in:

#### Laravel 9.X, 10.X
Add in `app/Exceptions/Handler.php` in the method `render`:
```php
ExceptionHandler::render($e, $request);
```
#### Laravel version ^11.X
Add in `bootstrap/app.php`:
```php
->withExceptions(function (Exceptions $exceptions) {
    ExceptionHandler::render($exceptions);
})
```

### Image
This class facilitates some actions on images.
```php
// Get extension from Image url
public static function getExtensionImageFromUrl(
	string $url
): ?string

// Destroy a image
public static function destroy(
	string $src
): bool

// Download image from url
public static function downloadFromUrl(
	string $url,
	string $fileName
): void
```
### Password
This class facilitates some actions with passwords.
```php
// Get secure rules for passwords
public static function rule(
	int $min_size = self::PASSWORD_LENGTH
): \Illuminate\Validation\Rules\Password

// Generate a secure and unleaked password
public static function generateSecurePassword(
    int $length = self::PASSWORD_LENGTH,
    int $max_retries = self::MAX_RETRIES
): string

// Checks if the given password is already leaked
public static function isLeakedPassword(
    string $password
): bool

```
### Text
This class facilitates some actions on texts.
```php
// Sanitize a text
public static function sanitize(
	string $text,
	string $divider = '-'
): string
```
### Time
This class facilitates some actions with time.
```php
// Parse time from seconds to humans
public static function parseTimeForHumans(
	int $inputSeconds,
	string $unitMin = 's'
): string
```
### Zip
This class facilitates some actions with zip files.
```php
// Create a zip file of a folder with hierarchy subfolders
public static function create(
	string $zipFile,
	string $sourcePath
): ?string
```

## Rules
- [SlugRule](#slugRule)

### SlugRule
Get a slug rule to validate slugs in <strong>RequestForm</strong>
