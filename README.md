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
        
7. copy :

        use Tymon\JWTAuth\Contracts\JWTSuject 
   
   then paste it before the User model. copy too :
   
        public function getJWTIdentifier() {
        return $this->getKey();
        }
    
        public function getJWTCustomClaims() {
            return [];
        }
    
   like the example below or you can directly copy :

        <?php

        namespace App\Models;

        use Illuminate\Contracts\Auth\MustVerifyEmail;
        use Illuminate\Database\Eloquent\Factories\HasFactory;
        use Illuminate\Foundation\Auth\User as Authenticatable;
        use Illuminate\Notifications\Notifiable;
        use Laravel\Sanctum\HasApiTokens;
        use Tymon\JWTAuth\Contracts\JWTSubject;

        class User extends Authenticatable implements JWTSubject
        {
            use HasApiTokens, HasFactory, Notifiable;

            /**
             * The attributes that are mass assignable.
             *
             * @var array<int, string>
             */
            protected $fillable = [
                'name',
                'email',
                'password',
            ];

            /**
             * The attributes that should be hidden for serialization.
             *
             * @var array<int, string>
             */
            protected $hidden = [
                'password',
                'remember_token',
            ];

            /**
             * The attributes that should be cast.
             *
             * @var array<string, string>
             */
            protected $casts = [
                'email_verified_at' => 'datetime',
            ];

            /**
             * Get the identifier that will be stored in the subject claim of the JWT.
             *
             * @return mixed
             */
            public function getJWTIdentifier() {
                return $this->getKey();
            }
            /**
             * Return a key value array, containing any custom claims to be added to the JWT.
             *
             * @return array
             */
            public function getJWTCustomClaims() {
                return [];
            }    
        }








