# Laravel-JWT-Auth-Login-Register

1. We will create REST API Register and Login using laravel JWT,, the command is as follows :

    $ composer create-project laravel/laravel Project-Name
  
2. wait until the installation process is complete, when finished go to the project folder :

    $ cd Project-Name
  
3. install JWT package :

    $ composer require tymon/jwt-auth:dev-develop --prefer-source
    
4. NNext add it in the providers and aliases section, open the config/app.php folder :
    
    providers : 
    
    $ Tymon\JWTAuth\Providers\LaravelServiceProvider::class,
    
    aliases : 
    
    $ 'JWTAuth' => Tymon\JWTAuth\Facades\JWTAuth::class,
    
    $ 'JWTFactory' => Tymon\JWTAuth\Facades\JWTFactory::class,
