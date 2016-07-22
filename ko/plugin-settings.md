# 사이트 관리 페이지 추가하기

XE는 사이트 관리자 또는 관리 등급을 가진 회원만 접근할 수 있는 '사이트 관리 영역'을 가지고 있습니다. 사이트 관리 영역은 사이트 관리에 필요한 다양한 '사이트 관리 페이지'들로 구성되어 있습니다. 사이트 관리 영역은 일반적으로 `http://<도메인>/settings`로 접근할 수 있습니다.


플러그인도 사이트 관리 영역에 플러그인을 위한 페이지를 추가할 수 있습니다.

## 라우트 등록

관리 페이지를 위한 라우트를 등록하는 방법은 일반 웹페이지의 라우트를 등록하는 방법과 크게 다르지 않습니다. 다만, `Route::group` 메소드 대신 `Route::settings` 메소드를 사용하면 됩니다. 

```php
  Route::settings($uri, function() {
    Route::get('/', ...);
    Route::post('/', ...);
  });
```


사이트 관리 영역에 속하는 모든 페이지의 URL은 모두 첫번째 세그먼트로 `settings`를 가지게 됩니다. 라우트를 등록하는 코드는 플러그인 클래스의 `boot` 메소드에 등록하십시오.

```php
<?php
// plugins/my_plugin/plugin.php
namespace MyPlugin;

use Xpressengine\Plugin\AbstractPlugin;
use Route;
use Presenter;

class Plugin extends AbstractPlugin
{
  public function boot()
  {
      // 사이트 관리 페이지 추가
      // http://<domain>/settings/my_plugin url로 접근 가능
      Route::settings(static::getId(), function() {
        Route::get('/', function(){
          return Presenter::make(static::view('views.settings');
        }
      });
  }
  
}
```

사이트 관리 페이지들이 출력될 때에는 사이트 관리 영역용 테마를 적용한 후 출력돼야 합니다. `Presenter`를 사용하여 결과를 반환하십시오. 자동으로 사이트 관리 영역용 테마가 적용됩니다.


## 메뉴 추가

사이트 관리 영역의 화면 좌측에는 관리 메뉴 트리가 출력됩니다. 플러그인은 이 트리에 메뉴를 추가할 수 있습니다. `XeRegister` 파사드의 `push` 메소드를 사용하여 `settings/menu`에 메뉴 정보를 등록합니다.

```php
\XeRegister::push('settings/menu', $menuId, $menuInfo);
```

아래 코드는 사이트 관리 메뉴의 `컨텐츠` 메뉴 하위에 서브 메뉴로 `게시판` 메뉴를 추가하는 예제입니다.

```php
\XeRegister::push('settings/menu', 'contents.board', [
    'title' => '게시판',
    'display' => true,
    'description' => '',
    'ordering' => 2000
]);
```

첫번째 파라미터는 항상 `settings/menu`를 써야합니다.

두번째 파라미터는 메뉴의 아이디인 동시에 메뉴의 부모 메뉴를 지정하는 역할을 합니다. 부모메뉴의 아이디와 현재 메뉴의 아이디를 점(.)을 사용하여 연결해주십시오. 위 예제의 경우 `contents` 메뉴 하위에 `board` 메뉴를 추가합니다.

세번째 파라미터는 메뉴의 상세정보를 담은 배열입니다.

- `title`은 메뉴가 출력될 때 사용하는 메뉴의 이름입니다.

- `display`가 `true`이면 메뉴가 메뉴 트리에 출력됩니다. `false`의 경우 메뉴 트리에 출력되지 않습니다. 예를 들어 `회원 정보 수정`과 같은 메뉴는 메뉴 트리에는 출력되지 않지만, 메뉴로 등록되어 있습니다.




## 권한 추가


## 컨트롤러 구현