<a name="ddav"></a>
### <a name="disable-default-account-verification"></a>기본 계정 확인 사용 안 함

기본 템플릿을 사용 하면 사용자가 `Account.RegisterConfirmation` 계정 확인을 위해 링크를 선택할 수 있는 위치로 리디렉션됩니다. 기본값은 `Account.RegisterConfirmation` 테스트에 ***만*** 사용 되며 자동 계정 확인은 프로덕션 앱에서 사용 하지 않도록 설정 해야 합니다.

확인 된 계정을 요구 하 고 등록 시 즉시 로그인을 방지 하려면 `DisplayConfirmAccountLink = false` */Areas/Identity/Pages/Account/RegisterConfirmation.cshtml.cs*에서를 설정 합니다.

[!code-csharp[](~/security/authentication/identity/sample/WebApp3/Areas/Identity/Pages/Account/RegisterConfirmation.cshtml.cs?name=snippet&highlight=34)]