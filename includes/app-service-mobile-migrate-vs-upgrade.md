---
author: conceptdev
ms.service: app-service-mobile
ms.topic: include
ms.date: 08/23/2018
ms.author: crdun
ms.openlocfilehash: 767b7c591928385f6ecaf93db1aca7eaca0205b3
ms.sourcegitcommit: 9d7391e11d69af521a112ca886488caff5808ad6
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 10/25/2018
ms.locfileid: "50133369"
---
Det finns två alternativ för att flytta från Mobile Services till Apptjänst: migrering eller uppgradering. Något av alternativen får du åtkomst till alla värdfunktionerna i App Service:

* *Migrera* en tjänst ändrar bara den underliggande miljö som är värd för din mobila serverdelstjänst. Det krävs **utan kodändringar** mobila klienten eller mobila serverprojektet. Om du använder alternativet Automatisk migrering Detta bevarar dina **service.azure mobile.net** URL: en. 
* *Uppgradera* en tjänst har **kräver ändringar i koden** till både servern och klientprojekt, men gör att du kan dra nytta av nya mobila SDK-funktioner, till exempel förbättringar för autentisering och mer flexibilitet för din serverprojekt. 

