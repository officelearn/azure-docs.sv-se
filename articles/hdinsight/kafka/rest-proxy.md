---
title: Apache Kafka REST-proxy - Azure HDInsight
description: Lär dig hur du utför Apache Kafka-åtgärder med hjälp av en Kafka REST-proxy på Azure HDInsight.
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: hrasheed
ms.service: hdinsight
ms.topic: conceptual
ms.date: 04/01/2020
ms.openlocfilehash: 8997b385960c58b17747dfcfced74010af80550b
ms.sourcegitcommit: 980c3d827cc0f25b94b1eb93fd3d9041f3593036
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/02/2020
ms.locfileid: "80548216"
---
# <a name="interact-with-apache-kafka-clusters-in-azure-hdinsight-using-a-rest-proxy"></a>Interagera med Apache Kafka-kluster i Azure HDInsight med hjälp av en REST-proxy

Kafka REST Proxy kan du interagera med din Kafka kluster via en REST API över HTTP. Detta innebär att dina Kafka-klienter kan vara utanför ditt virtuella nätverk. Dessutom kan klienter ringa enkla HTTP-anrop för att skicka och ta emot meddelanden till Kafka-klustret, i stället för att förlita sig på Kafka-bibliotek. Den här självstudien visar hur du skapar ett REST-proxyaktiverat Kafka-kluster och tillhandahåller en exempelkod som visar hur du ringer anrop till REST-proxy.

## <a name="rest-api-reference"></a>Referens för REST-API

För fullständig specifikation av åtgärder som stöds av Kafka REST API, se [HDInsight Kafka REST Proxy API Reference](https://docs.microsoft.com/rest/api/hdinsight-kafka-rest-proxy).

## <a name="background"></a>Bakgrund

![Kafka REST proxyarkitektur](./media/rest-proxy/rest-proxy-architecture.png)

För fullständig specifikation av åtgärder som stöds av API, se [Apache Kafka REST Proxy API](https://docs.microsoft.com/rest/api/hdinsight-kafka-rest-proxy).

### <a name="rest-proxy-endpoint"></a>SLUTPUNKT FÖR REST-proxy

När du skapar ett HDInsight Kafka-kluster med REST-proxy skapas en ny offentlig slutpunkt för klustret, som du hittar i ditt HDInsight-kluster "Egenskaper" på Azure-portalen.

### <a name="security"></a>Säkerhet

Åtkomst till Kafka REST-proxyn hanteras med Azure Active Directory-säkerhetsgrupper. När du skapar Kafka-klustret med REST-proxyn aktiverad ska du tillhandahålla säkerhetsgruppen Azure Active Directory som ska ha åtkomst till REST-slutpunkten. De Kafka-klienter (program) som behöver åtkomst till REST-proxyn ska registreras i den här gruppen av gruppägaren. Gruppägaren kan göra detta via portalen eller via PowerShell.

Innan begäranden till REST-proxyslutpunkten ska klientprogrammet hämta en OAuth-token för att verifiera medlemskap i rätt säkerhetsgrupp. Hitta ett [exempel på klientprogram](#client-application-sample) nedan som visar hur du skaffar en OAuth-token. När klientprogrammet har OAuth-token måste de skicka den token i HTTP-begäran som gjorts till REST-proxyn.

> [!NOTE]  
> Mer information om AAD-säkerhetsgrupper finns i [Hantera app- och resursåtkomst med Hjälp av Azure Active Directory-grupper.](../../active-directory/fundamentals/active-directory-manage-groups.md) Mer information om hur OAuth-token fungerar finns i [Auktorisera åtkomst till Azure Active Directory-webbprogram med hjälp av kodbidragsflödet för OAuth 2.0](../../active-directory/develop/v1-protocols-oauth-code.md).

## <a name="prerequisites"></a>Krav

1. Registrera ett program med Azure AD. Klientprogrammen som du skriver för att interagera med Kafka REST-proxyn använder programmets ID och hemlighet för att autentisera till Azure.

1. Skapa en Azure AD-säkerhetsgrupp och lägg till programmet som du har registrerat med Azure AD i säkerhetsgruppen som "medlem" i gruppen. Den här säkerhetsgruppen används för att styra vilka program som tillåts interagera med REST-proxyn. Mer information om hur du skapar Azure AD-grupper finns i [Skapa en grundläggande grupp och lägga till medlemmar med Azure Active Directory](../../active-directory/fundamentals/active-directory-groups-create-azure-portal.md).

    Verifiera gruppen är av typen !["Säkerhet" Säkerhetsgrupp](./media/rest-proxy/rest-proxy-group.png)

    Verifiera att programmet är ![medlem i gruppvalideringsmedlemskap](./media/rest-proxy/rest-proxy-membergroup.png)

## <a name="create-a-kafka-cluster-with-rest-proxy-enabled"></a>Skapa ett Kafka-kluster med REST-proxy aktiverad

1. Under arbetsflödet för att skapa Kafka-kluster, på fliken "Säkerhet + nätverk", kontrollerar du alternativet Aktivera Kafka REST-proxy.

     ![Aktivera Kafka REST-proxy och välj säkerhetsgrupp](./media/rest-proxy/azure-portal-cluster-security-networking-kafka-rest.png)

1. Klicka på **Välj säkerhetsgrupp**. Välj den säkerhetsgrupp som du vill ha åtkomst till REST-proxyn i listan över säkerhetsgrupper. Du kan använda sökrutan för att hitta rätt säkerhetsgrupp. Klicka på knappen **Välj** längst ned.

     ![Aktivera Kafka REST-proxy och välj säkerhetsgrupp](./media/rest-proxy/azure-portal-cluster-security-networking-kafka-rest2.png)

1. Slutför de återstående stegen för att skapa klustret enligt beskrivningen i [Skapa Apache Kafka-kluster i Azure HDInsight med Azure-portalen](https://docs.microsoft.com/azure/hdinsight/kafka/apache-kafka-get-started).

1. När klustret har skapats går du till klusteregenskaperna för att registrera Kafka REST-proxy-URL:en.

     ![visa REST-proxy-URL](./media/rest-proxy/apache-kafka-rest-proxy-view-proxy-url.png)

## <a name="client-application-sample"></a>Exempel på klientprogram

Du kan använda pythonkoden nedan för att interagera med REST-proxyn i Kafka-klustret. Så här använder du kodexemplet:

1. Spara exempelkoden på en dator med Python installerat.
1. Installera nödvändiga python-beroenden `pip3 install adal` `pip install msrestazure`genom att köra och .
1. Ändra kodavsnittet *Konfigurera dessa egenskaper* och uppdatera följande egenskaper för din miljö:
    1.    *Klient-ID* – Azure-klienten där din prenumeration finns.
    1.    *Klient-ID* – ID för det program som du registrerade i säkerhetsgruppen.
    1.    *Client Secret* – Hemligheten för programmet som du registrerade i säkerhetsgruppen
    1.    *Kafkarest_endpoint* – hämta det här värdet från fliken "egenskaper" i klusteröversikten enligt beskrivningen i [distributionsavsnittet](#create-a-kafka-cluster-with-rest-proxy-enabled). Det bör vara i följande format -`https://<clustername>-kafkarest.azurehdinsight.net`
1. Kör python-filen från kommandoraden genom att köra python-filen`python <filename.py>`

Den här koden gör följande:

1. Hämtar en OAuth-token från Azure AD
1. Visar hur du gör en begäran till Kafka REST proxy

Mer information om hur du hämtar OAuth-token i python finns i [klassen Python AuthenticationContext](https://docs.microsoft.com/python/api/adal/adal.authentication_context.authenticationcontext?view=azure-python). Du kan se en fördröjning medan ämnen som inte skapas eller tas bort via Kafka REST-proxyn återspeglas där. Den här fördröjningen beror på uppdatering av cache.

```python
#Required python packages
#pip3 install adal
#pip install msrestazure

import adal
from msrestazure.azure_active_directory import AdalAuthentication
from msrestazure.azure_cloud import AZURE_PUBLIC_CLOUD
import requests

#--------------------------Configure these properties-------------------------------#
# Tenant ID for your Azure Subscription
tenant_id = 'ABCDEFGH-1234-1234-1234-ABCDEFGHIJKL'
# Your Client Application Id
client_id = 'XYZABCDE-1234-1234-1234-ABCDEFGHIJKL'
# Your Client Credentials
client_secret = 'password'
# kafka rest proxy -endpoint
kafkarest_endpoint = "https://<clustername>-kafkarest.azurehdinsight.net"
#--------------------------Configure these properties-------------------------------#

#getting token
login_endpoint = AZURE_PUBLIC_CLOUD.endpoints.active_directory
resource = "https://hib.azurehdinsight.net"
context = adal.AuthenticationContext(login_endpoint + '/' + tenant_id)

token = context.acquire_token_with_client_credentials(
    resource,
    client_id,
    client_secret)

accessToken = 'Bearer ' + token['accessToken']

print(accessToken)

# relative url
getstatus = "/v1/metadata/topics"
request_url = kafkarest_endpoint + getstatus

# sending get request and saving the response as response object
response = requests.get(request_url, headers={'Authorization': accessToken})
print(response.content)
```

Nedan ett annat exempel på hur du hämtar en token från Azure för REST-proxy med hjälp av ett curl-kommando. Observera att vi `resource=https://hib.azurehdinsight.net` behöver den angivna samtidigt som du får en token.

```cmd
curl -X POST -H "Content-Type: application/x-www-form-urlencoded" -d 'client_id=<clientid>&client_secret=<clientsecret>&grant_type=client_credentials&resource=https://hib.azurehdinsight.net' 'https://login.microsoftonline.com/<tenantid>/oauth2/token'
```

## <a name="next-steps"></a>Nästa steg

* [Kafka REST proxy API referensdokument](https://docs.microsoft.com/rest/api/hdinsight-kafka-rest-proxy/)
