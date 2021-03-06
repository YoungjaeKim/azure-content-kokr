<properties
	pageTitle="Xamarin.Forms 앱에서 모바일 앱에 대한 인증 시작"
	description="모바일 앱을 사용하여 AAD, Google, Facebook, Twitter, Microsoft 등의 다양한 ID 공급자를 통해 Xamarin Forms 앱 사용자를 인증하는 방법을 알아봅니다."
	services="app-service\mobile"
	documentationCenter="xamarin"
	authors="wesmc7777"
	manager="dwrede"
	editor=""/>

<tags
	ms.service="app-service-mobile"
	ms.workload="mobile"
	ms.tgt_pltfrm="mobile-xamarin"
	ms.devlang="dotnet"
	ms.topic="article"
	ms.date="05/05/2016"
	ms.author="wesmc"/>

# Xamarin.Forms 앱에 인증 추가

[AZURE.INCLUDE [app-service-mobile-selector-get-started-users](../../includes/app-service-mobile-selector-get-started-users.md)]

##개요

이 항목에서는 클라이언트 응용 프로그램에서 앱 서비스 모바일 앱의 사용자를 인증하는 방법을 보여 줍니다. 이 자습서에서는 앱 서비스가 지원하는 ID 공급자를 사용하여 Xamarin.Forms 빠른 시작 프로젝트에 인증을 추가합니다. 모바일 앱에서 인증이 완료되고 권한이 부여되고 나면 사용자 ID 값이 표시되고 제한된 테이블 데이터에 액세스할 수 있게 됩니다.

[Xamarin.Forms 앱 만들기](app-service-mobile-xamarin-forms-get-started.md) 자습서를 먼저 완료해야 합니다. 다운로드한 빠른 시작 서버 프로젝트를 사용하지 않는 경우 프로젝트에 인증 확장 패키지를 추가해야 합니다. 서버 확장 패키지에 대한 자세한 내용은 [Azure 모바일 앱용 .NET 백 엔드 서버 SDK 사용](app-service-mobile-dotnet-backend-how-to-use-server-sdk.md)을 참조하세요.

##인증을 위해 앱 등록 및 앱 서비스 구성

[AZURE.INCLUDE [app-service-mobile-register-authentication](../../includes/app-service-mobile-register-authentication.md)]

##사용 권한을 인증된 사용자로 제한

[AZURE.INCLUDE [app-service-mobile-restrict-permissions-dotnet-backend](../../includes/app-service-mobile-restrict-permissions-dotnet-backend.md)]


##이식 가능한 클래스 라이브러리에 인증 추가

모바일 앱은 로그인 인터페이스를 표시하고 데이터를 캐시하기 위해서 플랫폼 전용 `MobileServiceClient.LoginAsync` 메서드를 사용합니다. Xamarin Forms 프로젝트를 사용하여 인증하기 위해서 이식 가능한 클래스 라이브러리에 `IAuthenticate` 인터페이스를 정의합니다. 지원하려는 플랫폼마다 플랫폼 특정 프로젝트에 이 인터페이스를 구현합니다.

또한 이식 가능한 클래스 라이브러리에 정의된 사용자 인터페이스를 업데이트하며 이는 로그인 단추를 추가합니다. 사용자는 앱이 시작된 후에 인증하기 위해 이 단추를 클릭해야 합니다.

1. Visual Studio 또는 Xamarin Studio의 **이식 가능** 프로젝트에서 App.cs를 엽니다. 파일에 다음 `using` 문을 추가합니다.

		using System.Threading.Tasks;

2. App.cs에 `App` 클래스 정의 직전에 다음과 같은 `IAuthenticate` 인터페이스 정의를 추가합니다.

	    public interface IAuthenticate
	    {
	        Task<bool> Authenticate();
	    };

3. App.cs에 플랫폼 전용 구현으로 인터페이스를 초기화하도록 다음과 같은 정적 멤버를 추가합니다.

		public class App : Application
		{

	        public static IAuthenticate Authenticator { get; private set; }

	        public static void Init(IAuthenticate authenticator)
	        {
	            Authenticator = authenticator;
	        }

			...


4. **이식 가능** 프로젝트에서 TodoList.xaml.cs를 엽니다. 다음 플래그를 `TodoList` 클래스에 추가하여 사용자가 인증되었는지를 나타냅니다.

        bool authenticated = false;


5. TodoList.xaml.cs에서 `OnAppearing` 메서드를 업데이트하므로 사용자가 인증된 경우 항목을 새로 고칩니다.


        protected override async void OnAppearing()
        {
            base.OnAppearing();

            // Set syncItems to true in order to synchronize the data on startup when running in offline mode
            if (authenticated == true)
                await RefreshItems(true, syncItems: false);
        }

6. TodoList.xaml.cs에서 `TodoList` 클래스에 대한 생성자의 위쪽에 다음 로그인 단추를 정의하고 처리기를 클릭합니다...

        public TodoList()
        {
            InitializeComponent();

            manager = TodoItemManager.DefaultManager;

            var loginButton = new Button
            {
                Text = "Login",
                TextColor = Xamarin.Forms.Color.Black,
                BackgroundColor = Xamarin.Forms.Color.Lime,
            };
            loginButton.Clicked += loginButton_Clicked;

            Xamarin.Forms.StackLayout bp = buttonsPanel as StackLayout;
            Xamarin.Forms.StackLayout bpParentStack = bp.Parent.Parent as StackLayout;

            bpParentStack.Padding = new Xamarin.Forms.Thickness(10, 30, 10, 20);
            bp.Orientation = StackOrientation.Vertical;
            bp.Children.Add(loginButton);

			...

7. TodoList.xaml.cs에서 로그인 단추 클릭 이벤트에 다음 처리기를 추가합니다.

        async void loginButton_Clicked(object sender, EventArgs e)
        {
            if (App.Authenticator != null)
                authenticated = await App.Authenticator.Authenticate();

            // Set syncItems to true in order to synchronize the data on startup when running in offline mode
            if (authenticated == true)
                await RefreshItems(true, syncItems: false);
        }


8. 변경 내용을 저장하고 오류가 없도록 확인하는 이식 가능한 클래스 라이브러리 프로젝트를 빌드합니다.


##Android 앱에 인증 추가

이 섹션에서는 droid 프로젝트에 대한 인증을 추가합니다. Android 장치를 작업하지 않는 경우 이 섹션을 건너뛸 수 있습니다.

1. Visual Studio 또는 Xamarin Studio에서 **droid** 프로젝트를 마우스 오른쪽 단추로 클릭하고 **시작 프로젝트로 설정**을 클릭합니다.

2. 계속하여 디버거에서 프로젝트를 실행하고 앱이 시작된 후 상태 코드 401(인증되지 않음)의 처리되지 않은 예외가 발생하는지 확인합니다. 백 엔드에서 액세스를 인증된 사용자만으로 제한했기 때문에 이런 상황이 발생합니다.

3. 그 다음 droid 프로젝트에서 MainActivity.cs를 열고 다음 `using` 문을 추가합니다.

		using Microsoft.WindowsAzure.MobileServices;
		using System.Threading.Tasks;

4. `IAuthenticate` 인터페이스를 구현하도록 `MainActivity` 클래스를 업데이트합니다.

		public class MainActivity : global::Xamarin.Forms.Platform.Android.FormsApplicationActivity, IAuthenticate


5. `IAuthenticate` 인터페이스를 지원하도록 아래 보이는 `MobileServiceUser` 필드 및 `Authenticate` 메서드를 추가하여 `MainActivity` 클래스를 업데이트합니다.

	Facebook 대신 다른 `MobileServiceAuthenticationProvider`를 사용하려면 해당 사항도 변경합니다.

		// Define a authenticated user.
		private MobileServiceUser user;

        public async Task<bool> Authenticate()
        {
            var success = false;
            try
            {
                // Sign in with Facebook login using a server-managed flow.
                user = await TodoItemManager.DefaultManager.CurrentClient.LoginAsync(this,
                    MobileServiceAuthenticationProvider.Facebook);
                CreateAndShowDialog(string.Format("you are now logged in - {0}",
                    user.UserId), "Logged in!");

                success = true;
            }
            catch (Exception ex)
            {
                CreateAndShowDialog(ex.Message, "Authentication failed");
            }
            return success;
        }

        private void CreateAndShowDialog(String message, String title)
        {
            AlertDialog.Builder builder = new AlertDialog.Builder(this);

            builder.SetMessage(message);
            builder.SetTitle(title);
            builder.Create().Show();
        }


6. 앱을 로드하기 전에 인증자를 초기화하도록 `MainActivity` 클래스의 `OnCreate` 메서드를 업데이트합니다.

        App.Init((IAuthenticate)this);

        LoadApplication(new App());



7. 앱을 다시 빌드하고 실행합니다. 선택한 인증 공급자를 사용하여 로그인하고 인증된 사용자로 테이블에 액세스할 수 있는지 확인합니다.



##iOS 앱에 인증 추가

이 섹션에서는 iOS 프로젝트에 대한 인증을 추가합니다. iOS 장치를 작업하지 않는 경우 이 섹션을 건너뛸 수 있습니다.

1. Visual Studio 또는 Xamarin Studio에서 **iOS** 프로젝트를 마우스 오른쪽 단추로 클릭하고 **시작 프로젝트로 설정**을 클릭합니다.

2. 계속하여 디버거에서 프로젝트를 실행하고 앱이 시작된 후 상태 코드 401(인증되지 않음)의 처리되지 않은 예외가 발생하는지 확인합니다. 백 엔드에서 액세스를 인증된 사용자만으로 제한했기 때문에 이런 상황이 발생합니다.

3. 그 다음 iOS 프로젝트에서 AppDelegate.cs를 열고 다음 `using` 문을 추가합니다.

		using Microsoft.WindowsAzure.MobileServices;
		using System.Threading.Tasks;

4. `IAuthenticate` 인터페이스를 구현하도록 `AppDelegate` 클래스를 업데이트합니다.

		public partial class AppDelegate : global::Xamarin.Forms.Platform.iOS.FormsApplicationDelegate, IAuthenticate


5. `IAuthenticate` 인터페이스를 지원하도록 아래 보이는 `MobileServiceUser` 필드 및 `Authenticate` 메서드를 추가하여 `AppDelegate` 클래스를 업데이트합니다.

	Facebook 대신 다른 `MobileServiceAuthenticationProvider`를 사용하려면 해당 사항도 변경합니다.

		// Define a authenticated user.
		private MobileServiceUser user;

        public async Task<bool> Authenticate()
        {
            var success = false;
            try
            {
                // Sign in with Facebook login using a server-managed flow.
                if (user == null)
                {
                    user = await TodoItemManager.DefaultManager.CurrentClient.LoginAsync(UIApplication.SharedApplication.KeyWindow.RootViewController,
                        MobileServiceAuthenticationProvider.Facebook);
                    if (user != null)
                    {
                        UIAlertView avAlert = new UIAlertView("Authentication", "You are now logged in " + user.UserId, null, "OK", null);
                        avAlert.Show();
                    }
                }

                success = true;
            }
            catch (Exception ex)
            {
                UIAlertView avAlert = new UIAlertView("Authentication failed", ex.Message, null, "OK", null);
                avAlert.Show();
            }
            return success;
        }

6. 앱을 로드하기 전에 인증자를 초기화하도록 `AppDelegate` 클래스의 `FinishedLaunching` 메서드를 업데이트합니다.

        App.Init(this);

		LoadApplication (new App ());



7. 앱을 다시 빌드하고 실행합니다. 선택한 인증 공급자를 사용하여 로그인하고 인증된 사용자로 테이블에 액세스할 수 있는지 확인합니다.




##Windows 앱에 인증 추가

이 섹션에서는 WinApp 프로젝트에 대한 인증을 추가합니다. Windows 장치를 작업하지 않는 경우 이 섹션을 건너뛸 수 있습니다.

1. Visual Studio에서 **WinApp** 프로젝트를 마우스 오른쪽 단추로 누른 다음 **시작 프로젝트로 설정**을 클릭합니다.

2. 계속하여 디버거에서 프로젝트를 실행하고 앱이 시작된 후 상태 코드 401(인증되지 않음)의 처리되지 않은 예외가 발생하는지 확인합니다. 백 엔드에서 액세스를 인증된 사용자만으로 제한했기 때문에 이런 상황이 발생합니다.

3. 그 다음 WinApp 프로젝트에서 MainPage.xaml.cs를 열고 다음 `using` 문을 추가합니다. <*이식 가능한 클래스 라이브러리 네임스페이스*>를 이식 가능한 클래스 라이브러리의 네임스페이스로 바꿉니다.

		using Microsoft.WindowsAzure.MobileServices;
		using System.Threading.Tasks;
		using <Your portable class library namespace>;

4. `IAuthenticate` 인터페이스를 구현하도록 `MainPage` 클래스를 업데이트합니다.

	    public sealed partial class MainPage : IAuthenticate


5. `IAuthenticate` 인터페이스를 지원하도록 아래 보이는 `MobileServiceUser` 필드 및 `Authenticate` 메서드를 추가하여 `MainPage` 클래스를 업데이트합니다.

	Facebook 대신 다른 `MobileServiceAuthenticationProvider`를 사용하려면 해당 사항도 변경합니다.

        // Define a authenticated user.
        private MobileServiceUser user;

        public async Task<bool> Authenticate()
        {
            var success = false;
            try
            {
                // Sign in with Facebook login using a server-managed flow.
                if (user == null)
                {
                    user = await TodoItemManager.DefaultManager.CurrentClient.LoginAsync(MobileServiceAuthenticationProvider.Facebook);
					if (user != null)
					{
	                    var messageDialog = new Windows.UI.Popups.MessageDialog(
								string.Format("you are now logged in - {0}", user.UserId), "Authentication");
	                    messageDialog.ShowAsync();
					}
                }

                success = true;
            }
            catch (Exception ex)
            {
                var messageDialog = new Windows.UI.Popups.MessageDialog(ex.Message, "Authentication Failed");
                messageDialog.ShowAsync();
            }
            return success;
        }

6. 앱을 로드하기 전에 인증자를 초기화하도록 `MainPage` 클래스에 대한 생성자를 업데이트합니다. <*이식 가능한 클래스 라이브러리 네임스페이스*>를 이식 가능한 클래스 라이브러리의 네임스페이스로 바꿉니다.

        public MainPage()
        {
            this.InitializeComponent();

            <Your portable class library namespace>.App.Init(this);

            LoadApplication(new <Your portable class library namespace>.App());
        }



7. 앱을 다시 빌드하고 실행합니다. 선택한 인증 공급자를 사용하여 로그인하고 인증된 사용자로 테이블에 액세스할 수 있는지 확인합니다.


##Windows Phone 8.1 앱에 인증을 추가합니다.

이 섹션에서는 WinPhone81 프로젝트에 대한 인증을 추가합니다. Windows Phone 8.1 장치로 작업하지 않는 경우 이 섹션을 건너뛸 수 있습니다.

1. Visual Studio에서 **WinPhone81** 프로젝트를 마우스 오른쪽 단추로 누른 다음 **시작 프로젝트로 설정**을 클릭합니다.

2. 계속하여 디버거에서 프로젝트를 실행하고 앱이 시작된 후 상태 코드 401(인증되지 않음)의 처리되지 않은 예외가 발생하는지 확인합니다. 백 엔드에서 액세스를 인증된 사용자만으로 제한했기 때문에 이런 상황이 발생합니다.


3. 다음으로 WinPhone81 프로젝트에서 MainPage.xaml.cs를 열고 다음 `using` 문을 추가합니다. <*이식 가능한 클래스 라이브러리 네임스페이스*>를 이식 가능한 클래스 라이브러리의 네임스페이스로 바꿉니다.

		using Microsoft.WindowsAzure.MobileServices;
		using System.Threading.Tasks;
		using <Your portable class library namespace>;

4. `IAuthenticate` 인터페이스를 구현하도록 `MainPage` 클래스를 업데이트합니다.

	    public sealed partial class MainPage : IAuthenticate


5. `IAuthenticate` 인터페이스를 지원하도록 아래 보이는 `MobileServiceUser` 필드 및 `Authenticate` 메서드를 추가하여 `MainPage` 클래스를 업데이트합니다.

	Facebook 대신 다른 `MobileServiceAuthenticationProvider`를 사용하려면 해당 사항도 변경합니다.

        // Define a authenticated user.
        private MobileServiceUser user;

        public async Task<bool> Authenticate()
        {
            var success = false;
            try
            {
                // Sign in with Facebook login using a server-managed flow.
                if (user == null)
                {
                    user = await TodoItemManager.DefaultManager.CurrentClient.LoginAsync(MobileServiceAuthenticationProvider.Facebook);
					if (user != null)
					{
	                    var messageDialog = new Windows.UI.Popups.MessageDialog(
								string.Format("you are now logged in - {0}", user.UserId), "Authentication");
	                    messageDialog.ShowAsync();
					}
                }

                success = true;
            }
            catch (Exception ex)
            {
                var messageDialog = new Windows.UI.Popups.MessageDialog(ex.Message, "Authentication Failed");
                messageDialog.ShowAsync();
            }
            return success;
        }

6. 앱을 로드하기 전에 인증자를 초기화하도록 `MainPage` 클래스에 대한 생성자를 업데이트합니다. <*이식 가능한 클래스 라이브러리 네임스페이스*>를 이식 가능한 클래스 라이브러리의 네임스페이스로 바꿉니다.

        public MainPage()
        {
            this.InitializeComponent();

            this.NavigationCacheMode = NavigationCacheMode.Required;

            <Your portable class library namespace>.App.Init(this);

            LoadApplication(new <Your portable class library namespace>.App());
        }

7. Windows Phone에서 추가적으로 로그인을 완료해야 합니다. App.xaml.cs를 열고 다음 `using` 문 및 코드를 `App` 클래스의 `OnActivated` 처리기에 추가합니다.

	```
		using Microsoft.WindowsAzure.MobileServices;
	```

		protected override void OnActivated(IActivatedEventArgs args)
		{
		    base.OnActivated(args);

		    if (args.Kind == ActivationKind.WebAuthenticationBrokerContinuation)
		    {
		        var client = TodoItemManager.DefaultManager.CurrentClient as MobileServiceClient;
		        client.LoginComplete(args as WebAuthenticationBrokerContinuationEventArgs);
		    }
		}



8. 앱을 다시 빌드하고 실행합니다. 선택한 인증 공급자를 사용하여 로그인하고 인증된 사용자로 테이블에 액세스할 수 있는지 확인합니다.

<!-- Images. -->

<!-- URLs. -->
[apns object]: http://go.microsoft.com/fwlink/p/?LinkId=272333

<!---HONumber=AcomDC_0511_2016-->