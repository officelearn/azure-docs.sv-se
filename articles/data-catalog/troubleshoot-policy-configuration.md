---
title: Så här felsöker du Azure Data Catalog
description: Den här artikeln beskriver vanliga felsökning frågor för Azure Data Catalog-resurser.
author: JasonWHowell
ms.author: jasonh
ms.service: data-catalog
ms.topic: troubleshooting
ms.date: 06/13/2019
ms.openlocfilehash: ed74e90e5e8ed55b75968f51cb50e6a1b4cdd75d
ms.sourcegitcommit: b7a44709a0f82974578126f25abee27399f0887f
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 06/18/2019
ms.locfileid: "67203511"
---
# <a name="troubleshooting-azure-data-catalog"></a>Felsökning av Azure Data Catalog

Den här artikeln beskriver vanliga felsökning frågor för Azure Data Catalog-resurser. 

## <a name="functionality-limitations"></a>Funktionen begränsningar

När du använder Azure Data Catalog, begränsas följande funktioner:

- Konton med typen **gästrollen** stöds inte. Du kan inte lägga till gästkonton som användare av Azure Data Catalog och gästanvändare kan inte använda portalen på www.azuredatacatalog.com.

- Det går inte att skapa Azure Data Catalog-resurser med hjälp av Azure Resource Manager-mallar eller Azure PowerShell-kommandon.

- Azure Data Catalog-resursen kan inte flyttas mellan Azure-klienter.

## <a name="azure-active-directory-policy-configuration"></a>Konfiguration av Azure Active Directory-principer

Det kan uppstå en situation då du kan logga in på Azure Data Catalog-portalen, men då du får ett felmeddelande när du försöker logga in till registreringsverktyget för datakällan. Det här felet kan uppstå när du är ansluten till företagets nätverk eller när du ansluter från utanför företagets nätverk.

Registreringsverktyget använder *formulärautentisering* för att validera användarinloggningar mot Azure Active Directory. För att inloggningen ska lyckas måste en Azure Active Directory-administratör aktivera formulärautentisering i *den globala autentiseringsprincipen*.

Med den globala autentiseringsprincipen kan du aktivera autentisering separat för intranät- och extranätsanslutningar, som du ser i följande bild. Inloggningsfel kan uppstå om formulärautentisering inte är aktiverat för det nätverk som du ansluter.

 ![Global Azure Active Directory-autentiseringsprincip](./media/troubleshoot-policy-configuration/global-auth-policy.png)

Mer information finns i [Konfigurera autentiseringsprinciper](/previous-versions/windows/it-pro/windows-server-2012-R2-and-2012/dn486781(v=ws.11)).

## <a name="next-steps"></a>Nästa steg

* [Skapa en Azure Data Catalog](data-catalog-get-started.md)
