<properties
pageTitle="논리 앱에서 Azure 서비스 버스 커넥터 사용 | Microsoft Azure"
description="Microsoft Azure 앱 서비스 논리 앱에서 Azure 서비스 버스 커넥터(커넥터) 사용을 시작"
services=""    
documentationCenter=""     
authors="msftman"    
manager="erikre"    
editor=""
tags="connectors"/>

<tags
ms.service="multiple"
ms.devlang="na"
ms.topic="article"
ms.tgt_pltfrm="na"
ms.workload="na"
ms.date="05/23/2016"
ms.author="deonhe"/>

# Azure 서비스 버스 커넥터 시작 

Azure 서비스 버스에 연결하여 메시지를 보내고 받습니다. 큐에 보내기, 항목에 보내기, 큐에서 수신, 구독에서 수신 등의 작업을 수행할 수 있습니다.

>[AZURE.NOTE] 이 버전의 문서는 논리 앱 2015-08-01-preview 스키마 버전에 적용됩니다.

Azure 서비스 버스를 사용하여 다음을 수행할 수 있습니다.

* 논리 앱 빌드  

논리 앱에 작업을 추가하려면 [논리 앱 만들기](../app-service-logic/app-service-logic-create-a-logic-app.md)를 참조하세요.

## 트리거 및 작업에 대한 정보

Azure 서비스 버스 커넥터는 작업으로 사용할 수 있으며 트리거를 가지고 있습니다. 모든 커넥터는 JSON 및 XML 형식의 데이터를 지원합니다.

 Azure 서비스 버스 커넥터에서는 다음과 같은 작업 및/또는 트리거를 사용할 수 있습니다.

### Azure 서비스 버스 작업
다음 작업을 수행할 수 있습니다.

|작업|설명|
|--- | ---|
|SendMessage|Azure 서비스 버스 큐나 항목으로 메시지를 보냅니다.|
### Azure 서비스 버스 트리거
다음 이벤트를 수신할 수 있습니다.

|트리거 | 설명|
|--- | ---|
|GetMessageFromQueue|Azure 서비스 버스 큐에서 새 메시지를 가져옵니다.|
|GetMessageFromTopic|Azure 서비스 버스 항목 구독에서 새 메시지를 가져옵니다.|


## Azure 서비스 버스에 대한 연결 만들기
Azure 서비스 버스 커넥터를 사용하려면 먼저 **연결**을 만든 다음 이러한 속성에 대한 세부 정보를 제공합니다.

>[AZURE.INCLUDE [ServiceBus에 대한 연결을 만드는 단계](../../includes/connectors-create-api-servicebus.md)]

>[AZURE.TIP] 다른 논리 앱에서 이 연결을 사용할 수 있습니다.

## Azure 서비스 버스 REST API 참조
#### 이 문서 적용 버전: 1.0


### Azure 서비스 버스 큐나 항목으로 메시지를 보냅니다.
**```POST: /{entityName}/messages```**로 바꿉니다.



| 이름| 데이터 형식|필수|위치|기본값|설명|
| ---|---|---|---|---|---|
|message| |yes|body|없음|서비스 버스 메시지|
|entityName|string|yes|path|없음|큐 또는 항목의 이름입니다.|


### 다음은 가능한 응답입니다.

|이름|설명|
|---|---|
|200|확인|
|기본값|작업이 실패했습니다.|
------



### Azure 서비스 버스 큐에서 새 메시지를 가져옵니다.
**```GET: /{queueName}/messages/head```**로 바꿉니다.



| 이름| 데이터 형식|필수|위치|기본값|설명|
| ---|---|---|---|---|---|
|queueName|string|yes|path|없음|큐의 이름입니다.|


### 다음은 가능한 응답입니다.

|이름|설명|
|---|---|
|200|확인|
|기본값|작업이 실패했습니다.|
------



### Azure 서비스 버스 항목 구독에서 새 메시지를 가져옵니다.
**```GET: /{topicName}/subscriptions/{subscriptionName}/messages/head```**로 바꿉니다.



| 이름| 데이터 형식|필수|위치|기본값|설명|
| ---|---|---|---|---|---|
|topicName|string|yes|path|없음|항목의 이름입니다.|
|subscriptionName|string|yes|path|없음|항목 구독의 이름입니다.|


### 다음은 가능한 응답입니다.

|이름|설명|
|---|---|
|200|확인|
|기본값|작업이 실패했습니다.|
------



## 개체 정의: 

 **ServiceBusMessage**: 메시지는 콘텐츠와 속성으로 구성됩니다.

ServiceBusMessage에 대한 필수 속성:

ContentTransferEncoding

**모든 속성**:


| 이름 | 데이터 형식 |
|---|---|
|ContentData|string|
|ContentType|string|
|ContentTransferEncoding|string|
|속성|object|


## 다음 단계
[논리 앱 만들기](../app-service-logic/app-service-logic-create-a-logic-app.md)

<!---HONumber=AcomDC_0525_2016-->