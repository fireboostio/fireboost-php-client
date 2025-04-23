# PHP Client for Fireboost.io

This PHP client allows you to communicate with the Fireboost API. For login credential extraction, you can use the [php-encryptor](https://github.com/fireboostio/php-encryptor) repository.

## Installation

You can install the PHP client via Composer:
```bash
composer require fireboostio/php-client
```

## Usage

### Authentication and Obtaining JWT Token

To authenticate and obtain a JWT token for further communication, use the following code snippets:

#### Without the Encryptor Repository

```PHP
use FireboostIO\Api\DefaultApi;
use FireboostIO\Model\LoginInput;

$api = new DefaultApi();
$response = $api->login(new LoginInput([
    'client_id' => 'your_client_id',
    'encrypted_api_key' => 'your_encrypted_api_key'
]));
$jwt = $response->getJwtToken();
```

#### With the Encryptor Repository

```PHP
use FireboostIO\Api\DefaultApi;
use Fireboostio\Encryptor\CredentialExtractor;
use FireboostIO\Model\LoginInput;

$encryptor = new CredentialExtractor();
$loginInputData = $encryptor->getLoginInputData($fireboostioKey);

$api = new DefaultApi();
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

$api = new FireboostIO\Api\DefaultApi(null, $config);
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
    'store_id' => 'test',
    'api_key' => 'test',
    'content' => json_encode(['whatever' => 'you like']),
    'is_public' => true // or false
]));
```

### Read a Public Cache (Non-Secure Endpoint)
```PHP
$response = $api->publicGetCache($clientId, 'cache_key');
var_dump($response->getResult()[0]);
```

### Request read and write statistic data
```PHP
$response = $api->getTrackingData();
var_dump($response->getResult());
```

## LICENSE

This project is licensed under the MIT License. See the [LICENSE](https://github.com/fireboostio/php-client/blob/main/LICENSE) file for details.

