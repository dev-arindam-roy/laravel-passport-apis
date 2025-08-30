```php

namespace App\Http\Controllers;

use Illuminate\Http\Request;
use App\Models\User;
use Auth;
use Hash;
use DB;

class ApiController extends Controller
{
    public function createAccount(Request $request)
    {
        $user = new User;
        $user->name = $request->input('name');
        $user->email = $request->input('email');
        $user->password = Hash::make($request->input('password'));
        if ($user->save()) {
            $token = $user->createToken('onex')->accessToken;
            return response()->json(['success' => 1, 'data' => array('token' => $token, 'user' => $user)]);
        }
        return response()->json(['success' => 0, 'data' => array()]);
    }

    public function login(Request $request)
    {
        $user = User::where('email', $request->input('email'))->first();
        if (empty($user)) {
            return response()->json(['success' => 0, 'data' => array()]);
        }
        if (!Hash::check($request->input('password'), $user->password)) {
            return response()->json(['success' => 0, 'data' => array()]);
        }
        $token = $user->createToken('onex')->accessToken;
        return response()->json(['success' => 1, 'data' => array('token' => $token, 'user' => $user)]);
    }

    public function myProfile(Request $request)
    {
        $user = auth()->guard('api')->user();
        $token = auth()->guard('api')->user()->token();
        return response()->json(['success' => 1, 'data' => array('token' => $token, 'user' => $user)]);

        //return auth()->guard('api')->user()->token();

        return auth()->guard('api')->user()->token()->revoke();
    }

    public function logout(Request $request)
    {
        auth()->guard('api')->user()->token()->revoke();
        return response()->json(['success' => 1, 'data' => array()]);
    }
}

```
