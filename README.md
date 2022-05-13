# Laravel-JWT-Auth-Login-Register

1. We will create REST API Register and Login using laravel JWT,, the command is as follows :
    
        composer create-project laravel/laravel Project-Name
  
2. wait until the installation process is complete, when finished go to the project folder :

        cd Project-Name
  
3. install JWT package :

        composer require tymon/jwt-auth:dev-develop --prefer-source
  
4. Next add it in the providers and aliases section, open the `config/app.php` folder :
    
    providers : 
    
        Tymon\JWTAuth\Providers\LaravelServiceProvider::class,
  
    aliases : 
    
        'JWTAuth' => Tymon\JWTAuth\Facades\JWTAuth::class,
        'JWTFactory' => Tymon\JWTAuth\Facades\JWTFactory::class,
  
5.  in the next step, publish the JWT package :  

        php artisan vendor:publish --provider="Tymon\JWTAuth\Providers\JWTAuthServiceProvider"
       
6. then create jwt:secret , To handle token encryption, generate a secret key by running the following command :

        php artisan jwt:secret
        
7. 







