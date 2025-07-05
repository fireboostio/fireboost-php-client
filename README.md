# PHP Client for Fireboost.io

This PHP client allows you to communicate with the Fireboost API. For login credential extraction, you can use the [php-encryptor](https://github.com/fireboostio/php-encryptor) repository.

## Installation

You can install the PHP client via Composer:
```bash
composer require fireboostio/fireboost-php-client
```

## Usage

### Authentication and Obtaining JWT Token

To authenticate and obtain a JWT token for further communication, use the following code snippets:

#### Without the Encryptor Repository

```PHP
use FireboostIO\Api\FireboostApi;
use FireboostIO\Model\LoginInput;

$api = new FireboostApi();
$response = $api->login(new LoginInput([
    'encrypted_api_key' => 'your_encrypted_api_key'
]));
$jwt = $response->getJwtToken();
```

#### With the Encryptor Repository

```PHP
use FireboostIO\Api\FireboostApi;
use Fireboostio\Encryptor\CredentialExtractor;
use FireboostIO\Model\LoginInput;

$encryptor = new CredentialExtractor();
$loginInputData = $encryptor->getLoginInputData($fireboostioKey);

$api = new FireboostApi();
$response = $api->login(new LoginInput($loginInputData));
$jwt = $response->getJwtToken();
```

### Setting the JWT Token for Secure API Calls

Once you have the JWT token, set it in the client configuration for further secure API calls:

```PHP
use FireboostIO\Configuration;

$config = new Configuration();
$config->setAccessToken($jwt);
$config->setApiKeyPrefix('bearer', 'Bearer');

$api = new FireboostIO\Api\FireboostApi(null, $config);
```

## API Examples

### Read a Cache (Secure Endpoint)
```PHP
$response = $api->getCache('cache_key');
var_dump($response->getResult()[0]);
```

### Write Cache (Secure Endpoint)
```PHP
use FireboostIO\Model\SetInput;

$response = $api->setCache(new SetInput([
    'cache_key' => 'test.com/news/post/1234',
    'content' => json_encode(['whatever' => 'you like']),
    'is_public' => true // or false
]));
```

### Read a Public Cache (Non-Secure Endpoint)
```PHP
$response = $api->publicGetCache('cache_key');
var_dump($response->getResult()[0]);
```

### Delete a Cache (Secure Endpoint)
```PHP
$response = $api->deleteCache('cache_key');
```

### Delete All Cache (Secure Endpoint)
```PHP
$response = $api->deleteAllCache();
```

### Get All Cache (Secure Endpoint)
```PHP
// Get the first page of cache entries
$response = $api->getAllCache();

// Get subsequent pages using the cursor
$cursor = $response->getCursor();
if ($cursor) {
    $nextPageResponse = $api->getAllCache($cursor);
}

// Access the cache entries
$cacheEntries = $response->getKeys();
foreach ($cacheEntries as $entry) {
    var_dump($entry);
}
```

## LICENSE

This project is licensed under the MIT License. See the [LICENSE](https://github.com/fireboostio/php-client/blob/main/LICENSE) file for details.
