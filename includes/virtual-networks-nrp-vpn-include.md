## VPN 게이트웨이 
VPN 게이트웨이 리소스를 사용하여 온-프레미스 데이터 센터와 Azure 간에 보안 연결을 만들 수 있습니다. 다음과 같은 세 가지 방법으로 VPN 게이트웨이 리소스를 구성할 수 있습니다.
 
- **지점 및 사이트 간** - 컴퓨터에서 VPN 클라이언트를 사용하여 VNET에 호스트된 Azure 리소스에 안전하게 액세스할 수 있습니다. 
- **다중 사이트 연결** - 온-프레미스 데이터 센터에서 VNET에 실행 중인 리소스에 안전하게 연결할 수 있습니다. 
- **VNET 간** - 동일한 지역 내의 Azure VNET 간이나 지역 간에 안전하게 연결하여 지리적 복제 기능을 사용하여 작업을 빌드할 수 있습니다.

VPN 게이트웨이의 키 속성은 다음과 같습니다.
 
- **게이트웨이 유형** - 동적으로 라우팅되거나 정적으로 라우팅된 게이트웨이입니다. 
- **VPN 클라이언트 주소 풀 접두사** - 지점 및 사이트 구성으로 연결 중인 클라이언트에 할당할 IP 주소입니다.

<!---HONumber=Oct15_HO3-->