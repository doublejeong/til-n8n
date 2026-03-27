### Context
PHP 학습의 첫 단계로, 가장 기본적인 문자열 출력 방식인 "Hello World" 예제를 통해 언어의 기본 구문 및 실행 환경을 이해합니다. 모든 프로그래밍 언어에서 "Hello World"는 해당 언어의 기본적인 작동 원리를 파악하고 개발 환경이 올바르게 설정되었는지 확인하는 데 중요한 역할을 합니다.

### Core
PHP에서 "Hello World!" 문자열을 출력하는 가장 일반적인 방법은 `echo` 문을 사용하는 것입니다. 다음 코드를 `.php` 파일로 저장한 후 실행할 수 있습니다.

```php
<?php
echo "Hello World!";
?>
```

이 코드를 `hello.php`로 저장한 후 터미널에서 다음과 같이 실행할 수 있습니다.
```bash
php hello.php
```

### Insight
위 PHP 코드는 `echo` 키워드를 사용하여 "Hello World!"라는 문자열을 표준 출력(Standard Output)으로 내보냅니다.
*   `<?php`와 `?>` 태그는 PHP 코드 블록의 시작과 끝을 나타냅니다. 이 태그 안에 있는 내용만 PHP 코드로 해석됩니다.
*   `echo`는 하나 이상의 문자열을 출력하는 데 사용되는 언어 구조(language construct)입니다. `print`와 유사하지만, `echo`는 반환 값이 없고 `print`는 항상 `1`을 반환한다는 미묘한 차이가 있습니다. 일반적으로 `echo`가 `print`보다 성능 면에서 약간 더 빠르다고 알려져 있습니다.
*   PHP 스크립트는 크게 두 가지 방식으로 실행될 수 있습니다.
    *   **CLI (Command Line Interface)**: 웹 서버 없이 터미널에서 `php [파일명]` 명령어를 사용하여 실행합니다. 개발 및 스크립팅 작업에 유용합니다.
    *   **웹 서버**: Apache, Nginx와 같은 웹 서버와 PHP-FPM 또는 모듈 형태로 연동하여 웹 브라우저를 통해 실행합니다. 웹 애플리케이션 개발의 일반적인 방식입니다.

**출처:** [PHP: echo - Manual](https://www.php.net/manual/en/function.echo.php)