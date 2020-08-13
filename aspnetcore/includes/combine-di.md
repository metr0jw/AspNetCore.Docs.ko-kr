<a name="csc"></a>

## <a name="combining-service-collection"></a>서비스 컬렉션 결합

몇 가지 서비스 컬렉션을 포함하는 다음 `ConfigureServices`를 살펴보겠습니다.

[!code-csharp[](~/fundamentals/configuration/index/samples/3.x/ConfigSample/Startup2.cs?name=snippet)]

관련 등록 그룹을 확장 메서드로 이동하여 서비스를 등록할 수 있습니다. 예를 들어, 구성 서비스는 다음 클래스에 추가됩니다.

[!code-csharp[](~/fundamentals/configuration/index/samples/3.x/ConfigSample/Options/MyConfgServiceCollectionExtensions.cs)]

나머지 서비스는 유사한 클래스에 등록됩니다. 다음 `ConfigureServices`는 새 확장 메서드를 사용하여 서비스를 등록합니다.

[!code-csharp[](~/fundamentals/configuration/index/samples/3.x/ConfigSample/Startup4.cs?name=snippet)]

***참고:*** 각 `services.Add{SERVICE_NAME}` 확장 메서드는 서비스를 추가하고 잠재적으로 구성합니다. 예를 들어 <xref:Microsoft.Extensions.DependencyInjection.MvcServiceCollectionExtensions.AddControllersWithViews%2A>는 뷰가 있는 MVC 컨트롤러에 필요한 서비스를 추가합니다. <xref:Microsoft.Extensions.DependencyInjection.MvcServiceCollectionExtensions.AddRazorPages%2A>는 Razor Pages에 필요한 서비스를 추가합니다. 앱에서 이 명명 규칙을 따르는 것이 좋습니다. 확장 메서드를 [Microsoft.Extensions.DependencyInjection](/dotnet/api/microsoft.extensions.dependencyinjection) 네임스페이스에 배치하여 서비스 등록 그룹을 캡슐화합니다.