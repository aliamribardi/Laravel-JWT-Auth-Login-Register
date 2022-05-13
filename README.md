# Laravel-JWT-Auth-Login-Register

1. We will create REST API Register and Login using laravel JWT,, the command is as follows :
    
       composer create-project laravel/laravel Project-Name
  
2. wait until the installation process is complete, when finished go to the project folder :

       cd Project-Name
  
3. after creating new Laravel application, now to save registration data, user has to create database in MySQL and add database name, username and password in `.env` file :

        DB_CONNECTION=mysql
        DB_HOST=127.0.0.1
        DB_PORT=3306
        DB_DATABASE=jwt (customized)
        DB_USERNAME=root (customized)
        DB_PASSWORD= (customized)

4. install JWT package :

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

8. Place the following code in the `config/auth.php` file :

        <?php
        return [
            'defaults' => [
                'guard' => 'api',
                'passwords' => 'users',
            ],

            'guards' => [
                'web' => [
                    'driver' => 'session',
                    'provider' => 'users',
                ],
                'api' => [
                    'driver' => 'jwt',
                    'provider' => 'users',
                    'hash' => false,
                ],
            ],

9. Then create a UserController to create the register and login functions :

        php artisan make:controller UserController

10. After that open the UserController in the `app/Http/Controllers/UserController.php` folder and follow the code below :

        <?php

        namespace App\Http\Controllers;

        use Illuminate\Http\Request;
        use App\Models\User;
        use Illuminate\Support\Facades\Hash;
        use Illuminate\Support\Facades\Validator;
        use JWTAuth;
        use Tymon\JWTAuth\Exceptions\JWTException;

        class UserController extends Controller
        {
            public function login(Request $request)
            {
                $credentials = $request->only('email', 'password');

                try {
                    if (! $token = JWTAuth::attempt($credentials)) {
                        return response()->json(['error' => 'invalid_credentials'], 400);
                    }
                } catch (JWTException $e) {
                    return response()->json(['error' => 'could_not_create_token'], 500);
                }

                return response()->json(compact('token'));
            }

            public function register(Request $request)
            {
                $validator = Validator::make($request->all(), [
                    'name' => 'required|string|max:255',
                    'email' => 'required|string|email|max:255|unique:users',
                    'password' => 'required|string|min:6|confirmed',
                ]);

                if($validator->fails()){
                    return response()->json($validator->errors()->toJson(), 400);
                }

                $user = User::create([
                    'name' => $request->get('name'),
                    'email' => $request->get('email'),
                    'password' => Hash::make($request->get('password')),
                ]);

                $token = JWTAuth::fromUser($user);

                return response()->json(compact('user','token'),201);
            }

            public function getAuthenticatedUser()
            {
                try {

                    if (! $user = JWTAuth::parseToken()->authenticate()) {
                        return response()->json(['user_not_found'], 404);
                    }

                } catch (Tymon\JWTAuth\Exceptions\TokenExpiredException $e) {

                    return response()->json(['token_expired'], $e->getStatusCode());

                } catch (Tymon\JWTAuth\Exceptions\TokenInvalidException $e) {

                    return response()->json(['token_invalid'], $e->getStatusCode());

                } catch (Tymon\JWTAuth\Exceptions\JWTException $e) {

                    return response()->json(['token_absent'], $e->getStatusCode());

                }

                return response()->json(compact('user'));
            }

        }

11. then create routing in `route/api.php` :

        <?php

        use Illuminate\Http\Request;
        use Illuminate\Support\Facades\Route;
        use App\Http\Controllers\UserController;

        /*
        |--------------------------------------------------------------------------
        | API Routes
        |--------------------------------------------------------------------------
        |
        | Here is where you can register API routes for your application. These
        | routes are loaded by the RouteServiceProvider within a group which
        | is assigned the "api" middleware group. Enjoy building your API!
        |
        */

        Route::middleware('auth:sanctum')->get('/user', function (Request $request) {
            return $request->user();
        });

        Route::post('register', [UserController::class, 'register']);
        Route::post('login', [UserController::class, 'login']);
        Route::post('logout', [UserController::class, 'logout']);
        Route::get('user', [UserController::class, 'getAuthenticatedUser'])->middleware('jwt.verify');

12. aa









