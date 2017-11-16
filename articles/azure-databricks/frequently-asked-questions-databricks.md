---
title: "Azure Databricks: Vanliga frågor och hjälper dig | Microsoft Docs"
description: "Få svar på vanliga frågor och felsökningsinformation om Azure Databricks."
services: azure-databricks
documentationcenter: 
author: nitinme
manager: cgronlun
editor: cgronlun
ms.service: azure-databricks
ms.workload: big-data
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 11/15/2017
ms.author: nitinme
ms.openlocfilehash: b6b001087cba5f8550d4fea3e4a2f7c1c865beae
ms.sourcegitcommit: afc78e4fdef08e4ef75e3456fdfe3709d3c3680b
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 11/16/2017
---
# <a name="azure-databricks-preview-common-questions-and-help"></a>Förhandsgranskning, Azure Databricks: Vanliga frågor och hjälp

Den här artikeln visar upp frågor som du kanske relaterar till Azure Databricks. Dessutom visas några vanliga problem som kan uppstå när du använder Azure Databricks. Läs mer på Azure Databricks [vad är Azure Databricks?](what-is-azure-databricks.md) 

## <a name="common-questions"></a>Vanliga frågor

Det här avsnittet innehåller vanliga frågor rör Azure Databricks.

### <a name="can-i-use-my-own-keys-for-local-encryption"></a>Kan jag använda min egen nycklar för kryptering av lokala? 
I den aktuella versionen stöds med egna nycklar från Azure Keyvault inte. 

### <a name="can-i-use-azure-vnets-with-azure-databricks"></a>Kan jag använda Azure Vnet med Azure Databricks?
Ett nytt virtuellt nätverk skapas som en del av Azure Databricks etablering. Som en del av den här versionen kan använda du inte din egen Azure VNET.

### <a name="how-do-i-access-azure-data-lake-store-from-a-notebook"></a>Hur kommer jag åt Azure Data Lake Store från en bärbar dator? 

1. Etablera ett huvudnamn för tjänsten och registrera sin nyckel i Azure Active Directory.
2. Tilldela behörighet till tjänstens huvudnamn i Azure Data Lake Store.
3. Använd autentiseringsuppgifter för tjänstens huvudnamn i anteckningsboken för att komma åt en fil i Azure Data Lake Store.

Mer information finns i [Använd Data Lake Store med Azure Databricks](https://docs.azuredatabricks.net/spark/latest/data-sources/azure/azure-storage.html#azure-data-lake-store).

## <a name="troubleshooting"></a>Felsökning

Det här avsnittet beskrivs hur du felsöker vanliga problem med Azure Databricks.

### <a name="issue-your-account-email-does-not-have-owner-or-contributor-role-on-the-databricks-workspace-resource-in-the-azure-portal"></a>Problem: Kontot {e-post} inte har rollen som ägare eller deltagare på resursen Databricks arbetsytan i Azure-portalen.

**Felmeddelande**

Ditt konto {e-post} har inte ägare eller deltagare roll på resursen Databricks arbetsytan i Azure-portalen. Det här felet kan också inträffa om du är en gästanvändaren i klienten. Be administratören att ge åtkomst till, eller lägga till dig som en användare direkt i arbetsytan Databricks. 

**Lösning**

För att initiera klienten, måste du vara inloggad som en användare med klienten, inte en gästanvändare. Du måste också ha deltagarrollen på resursen Databricks arbetsytan. Du kan ge en användaråtkomst från den **åtkomstkontroll (IAM)** fliken i din Azure Databricks arbetsyta i Azure-portalen.

### <a name="issue-your-account-email-has-not-been-registered-in-databricks"></a>Problem: Kontot {e-post} har inte registrerats i Databricks 

**Lösning**

Om du inte har skapat arbetsytan och du läggs till som en användare i arbetsytan kontaktar du den person som skapade arbetsytan för att lägga till dig via Azure Databricks administratörskonsolen. Instruktioner finns i [lägga till och hantera användare](https://docs.azuredatabricks.net/administration-guide/admin-settings/users.html). Om arbetsytan skapades och fortfarande det här felet visas, kan du använda kommandot ”initiera arbetsytan” igen från Azure-portalen.

### <a name="issue-cloud-provider-launch-failure-a-cloud-provider-error-was-encountered-while-setting-up-the-cluster"></a>Problem: Molnet starta Providerfel: ett moln provider-fel uppstod vid konfigurera klustret.

**Felmeddelande**

Startfel för moln-Provider: En molnet providern fel uppstod när du konfigurerar klustret. Se guiden Databricks för mer information. Azure-felkod: PublicIPCountLimitReached. Azure-felmeddelande: Det går inte att skapa mer än 60 offentliga IP-adresser för den här prenumerationen i den här regionen.

**Lösning**

Azure Databricks kluster använder en offentlig IP-adress per nod. Om din prenumeration har redan använts alla dess offentliga IP-adresser, bör du [begäran om att öka kvoten](https://docs.microsoft.com/en-us/azure/azure-supportability/resource-manager-core-quotas-request). Välj **kvot** som den **typ av problem**, **nätverk ARM** som den **Kvottypen**, och begära en ökad offentliga IP-adressen kvot i  **Information om** (till exempel om du vill skapa ett kluster med 100 noder och gränsen är för närvarande 60 få gränsen utökad till 160).

## <a name="next-steps"></a>Nästa steg
Stegvisa instruktioner för att skapa en datafabrik för version 2 finns i följande kurser:

- [Snabbstart: Komma igång med Azure Databricks](quickstart-create-databricks-workspace-portal.md)
- [Vad är Azure Databricks?](what-is-azure-databricks.md)

