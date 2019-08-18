#### 后端实现

##### 一、安装依赖

```shell
pip install djangorestframework-jwt
```

##### 二、修改Django项目`setting.py`

如果全局JWT，去掉原先的Session认证

```python
REST_FRAMEWORK = {
    'DEFAULT_AUTHENTICATION_CLASSES': (
        'rest_framework_jwt.authentication.JSONWebTokenAuthentication',
        # 'rest_framework.authentication.SessionAuthentication',
        # 'rest_framework.authentication.BasicAuthentication',
    ),
    'DEFAULT_PERMISSION_CLASSES': (
        'rest_framework.permissions.IsAuthenticated',
    ),
}
```

##### 三、创建JWT-Token

```python
from rest_framework_jwt.settings import api_settings

def _generate_jwt_token(user)
    jwt_payload_handler = api_settings.JWT_PAYLOAD_HANDLER
    jwt_encode_handler = api_settings.JWT_ENCODE_HANDLER

		payload = jwt_payload_handler(user)
		token = jwt_encode_handler(payload)
		return token
```

##### 四、随login请求返回token

```python
@api_view(['POST'])
@permission_classes((permissions.AllowAny,))
def csrf_token(request):
  username = self.shift_data.get('username')
        password = self.shift_data.get('password')
        user = authenticate(username=username, password=password)
        if not user:
            raise exceptions.AuthenticationFailed()
        if not user.is_admin:
            raise exceptions.AuthenticationFailed()

        login(request, user)
        user_data = UserSerializer(user).data
        user_data['token'] = request.META['CSRF_COOKIE']
        user_data['jwt_token'] = _generate_jwt_token(self.request.user)
    return Response({'token': token})
```

#### 前端实现

##### 一、给所有请求加上请求头`Authorization`: `JWT <your_token>`

官方文档 [http://getblimp.github.io/django-rest-framework-jwt/]

```javascript
import axios from 'axios'
import qs from 'qs'
import store from '@/store'

// create an axios instance
const service = axios.create({
  baseURL: process.env.VUE_APP_BASE_API, // url = base url + request url
  withCredentials: true, // send cookies when cross-domain requests
  crossDomain: true,
  timeout: 15000, // request timeout
  paramsSerializer(params) {
    return qs.stringify(params, { indices: false })
  },
})

// request interceptor
service.interceptors.request.use(
  config => {
    // if CSRF-Token
    if (store.getters.token) {
      config.headers['X-CSRFToken'] = getToken()
    }
		// if JWT Tokem
    if (store.getters.jwt_token) {
      config.headers['Authorization'] = 'JWT ' + store.getters.jwt_token
    }

    return config
  },
  error => {
    // do something with request error
    return Promise.reject(error)
  }
)
```