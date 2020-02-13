---
title: Apache Kafka REST-proxy – Azure HDInsight
description: Lär dig hur du utför Apache Kafka åtgärder med en Kafka REST-proxy på Azure HDInsight.
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: hrasheed
ms.service: hdinsight
ms.topic: conceptual
ms.date: 12/17/2019
ms.openlocfilehash: bc6859d29a574cea0d97989977ba9a333b20f6c4
ms.sourcegitcommit: 76bc196464334a99510e33d836669d95d7f57643
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 02/12/2020
ms.locfileid: "77157149"
---
# <a name="interact-with-apache-kafka-clusters-in-azure-hdinsight-using-a-rest-proxy"></a>Interagera med Apache Kafka kluster i Azure HDInsight med hjälp av en REST-proxy

Med Kafka REST-proxyn kan du interagera med ditt Kafka-kluster via en REST API över HTTP. Det innebär att dina Kafka-klienter kan ligga utanför det virtuella nätverket. Dessutom kan klienter göra enkla HTTP-anrop för att skicka och ta emot meddelanden till Kafka-klustret, i stället för att förlita dig på Kafka-bibliotek.  

## <a name="background"></a>Bakgrund

### <a name="architecture"></a>Arkitektur

Utan en REST-proxy måste Kafka-klienter finnas i samma VNet som Kafka-klustret eller ett peer-kopplat VNet. Med REST-proxyn kan du ansluta data producenter eller konsumenter som finns var som helst. Om du distribuerar REST-proxyn skapas en ny offentlig slut punkt för klustret, som du hittar i dina Portal inställningar.

![Kafka REST proxy-arkitektur](./media/rest-proxy/rest-proxy-architecture.png)

En fullständig specifikation av de åtgärder som stöds av API: et finns [Apache KAFKA rest proxy API](https://docs.microsoft.com/rest/api/hdinsight-kafka-rest-proxy).

### <a name="security"></a>Säkerhet

Åtkomst till Kafka REST proxy hanteras med Azure Active Directory säkerhets grupper. Mer information finns i [Hantera app-och resurs åtkomst med hjälp av Azure Active Directory grupper](https://docs.microsoft.com/azure/active-directory/fundamentals/active-directory-manage-groups).

När du skapar Kafka-klustret med REST proxy aktiverat, kommer du att tillhandahålla AAD-säkerhetsgruppen som ska ha åtkomst till REST-slutpunkten. Kafka-klienter (program) som behöver åtkomst till REST-proxyn ska registreras till den här gruppen av grupp ägaren. Grupp ägaren kan göra detta via portalen eller via PowerShell.

Innan du skapar begär anden till REST proxy-slutpunkten bör klient programmet hämta en OAuth-token för att kontrol lera medlemskapet i rätt säkerhets grupp. Mer information om hur OAuth-token fungerar finns i [ge åtkomst till Azure Active Directory webb program med hjälp av OAuth 2,0 kod tilldelnings flödet](../../active-directory/azuread-dev/v1-protocols-oauth-code.md). Ett exempel på hur du hämtar en OAuth-token i python finns i [exempel på klient program](#client-application-sample)

När klient programmet har OAuth-token måste de skicka den token i HTTP-förfrågan till REST-proxyn.

## <a name="prerequisites"></a>Förutsättningar

1. Registrera ett program med Azure AD. Klient programmen som du skriver för att interagera med Kafka REST-proxyn använder programmets ID och hemlighet för att autentisera till Azure.
1. Skapa en Azure AD-säkerhetsgrupp och Lägg till det program som du har registrerat med Azure AD i säkerhets gruppen. Den här säkerhets gruppen används för att kontrol lera vilka program som får interagera med REST-proxyn. Mer information om hur du skapar Azure AD-grupper finns i [skapa en grundläggande grupp och lägga till medlemmar med hjälp av Azure Active Directory](../../active-directory/fundamentals/active-directory-groups-create-azure-portal.md).

## <a name="create-a-kafka-cluster-with-rest-proxy-enabled"></a>Skapa ett Kafka-kluster med REST proxy aktiverat

1. Under arbets flödet för skapande av Kafka-kluster går du till fliken "säkerhet + nätverk" och markerar alternativet "Aktivera Kafka REST proxy".

     ![Aktivera Kafka REST proxy och välj säkerhets grupp](./media/rest-proxy/azure-portal-cluster-security-networking-kafka-rest.png)

1. Klicka på **Välj säkerhets grupp**. I listan över säkerhets grupper väljer du den säkerhets grupp som du vill ha åtkomst till REST-proxyn. Du kan använda sökrutan för att hitta rätt säkerhets grupp. Klicka på knappen **Välj** längst ned.

     ![Aktivera Kafka REST proxy och välj säkerhets grupp](./media/rest-proxy/azure-portal-cluster-security-networking-kafka-rest2.png)

1. Slutför de återstående stegen för att skapa klustret enligt beskrivningen i [skapa Apache Kafka kluster i Azure HDInsight med Azure Portal](https://docs.microsoft.com/azure/hdinsight/kafka/apache-kafka-get-started).

1. När klustret har skapats går du till kluster egenskaperna för att registrera Kafka REST proxy-URL: en.

     ![Visa URL för REST-proxy](./media/rest-proxy/apache-kafka-rest-proxy-view-proxy-url.png)

## <a name="client-application-sample"></a>Exempel på klient program

Du kan använda python-koden nedan för att interagera med REST-proxyn på ditt Kafka-kluster. Den här koden gör följande:

1. Hämtar en OAuth-token från Azure AD
1. Skapar ämnet som anges
1. Skickar meddelanden till ämnet
1. Använder meddelanden från det ämnet

Mer information om hur du hämtar OAuth-tokens i python finns i [python AuthenticationContext-klass](https://docs.microsoft.com/python/api/adal/adal.authentication_context.authenticationcontext?view=azure-python). Du kan se en fördröjning medan ämnen som inte skapas eller tas bort via Kafka REST proxy visas där. Den här fördröjningen beror på cache-uppdateringen.

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

Följ dessa steg om du vill använda kod exemplet:

1. Spara exempel koden på en dator med python installerat.
1. Installera nödvändiga python-beroenden genom att köra `pip3 install adal` och `pip install msrestazure`.
1. Ändra koden och uppdatera följande egenskaper för din miljö:
    1.  *Klient-ID* – Azure-klienten där din prenumeration är.
    1.  *Klient-ID* – ID för programmet som du registrerade i säkerhets gruppen.
    1.  *Klient hemlighet* – hemligheten för det program som du registrerade i säkerhets gruppen
    1.  *Kafkarest_endpoint* – hämta det här värdet från fliken "egenskaper" i kluster översikten enligt beskrivningen i [avsnittet distribution](#create-a-kafka-cluster-with-rest-proxy-enabled). Det ska ha följande format – `https://<clustername>-kafkarest.azurehdinsight.net`
3. Kör python-filen från kommando raden genom att köra `python <filename.py>`

## <a name="next-steps"></a>Nästa steg

* [Kafka REST proxy API-referens dokument](https://docs.microsoft.com/rest/api/hdinsight-kafka-rest-proxy/)
* [Självstudie: Använd Apache Kafka tillverkare och klient-API: er](apache-kafka-producer-consumer-api.md)