## Nginx Request UUID
Generates a variable `$request_uuid` with a unique UUID for each Nginx request. 

Super useful for tracking requests from Nginx all the way to your application.

## Example
### request_uuid1.conf
Will generate a UUID version 1 which includes a timestamp with a pseudo-random Mac Address.

`$request_uuid` = `330f21eb1f8111ecba62c8e76eb827c4`


### request_uuid4.conf
Will generate a pseudo-random UUID version 4.

`$request_uuid` = `e9d75e041a4d425ca7f7521428cb8ac0`

## Installation

 - Nginx plugin `nginx-extras` must be installed.

 - For **`request_uuid1.conf`**:
   - The `libuuid-tiny-perl` must be installed.

 - For **`request_uuid4.conf`**:
   - The `libossp-uuid-perl` must be installed.

 - Then simply copy `request_uuid1.conf` or `request_uuid4.conf` (depending on your preference) to `./nginx/conf.d` directory.

 - To check the configuration is successful run `nginx -t`

## Usage

### Add a header to each request
    server {     
        add_header request_uuid $request_uuid always;
    }
    
Will add a header called `request_uuid` (could be customized) with the UUID as a value.

### Pass the header to an application
    server {     
        location / {
            include uwsgi_params;
            uwsgi_pass_request_headers on;
            uwsgi_pass unix:/app/endpoint.sock;

            uwsgi_param    HTTP_REQUEST_UUID   $request_uuid;
        }
    }

### Use for logging
    http {
        log_format custom_json escape=json
            '{'
                '"request_uuid":"$request_uuid",'
                '"time_local":"$time_local",'
                '"remote_addr":"$remote_addr",'
                '"realip_remote_addr":"$realip_remote_addr",'
                '"remote_user":"$remote_user",'
                '"server_addr":"$server_addr",'
                '"server_port":"$server_port",'
                '"ssl_protocol":"$ssl_protocol",'
                '"ssl_cipher":"$ssl_cipher",'
                '"request_method":"$request_method",'
                '"scheme":"$scheme",'
                '"host":"$host",'
                '"request_uri":"$request_uri",'
                '"query_string":"$query_string",'
                '"server_protocol":"$server_protocol",'
                '"status":"$status",'
                '"bytes_out":"$body_bytes_sent",'
                '"bytes_in":"$upstream_response_length",'
                '"http_origin":"$http_origin",'
                '"http_referer":"$http_referer",'
                '"http_user_agent":"$http_user_agent",'
                '"gzip_ratio":"$gzip_ratio",'
                '"nginx_version":"$nginx_version",'
                '"http_x_forwarded_for":"$http_x_forwarded_for",'
                '"http_x_header":"$http_x_header",'
                '"http_cookie":"$http_cookie",'
                '"response_time":"$upstream_response_time",'
                '"request_time":"$request_time"'
            '}';
    }
