---
title: Så här konfigurerar du Azure Active Directory-principen för Azure Data Catalog
description: Du kan stöta på en situation där du kan logga in på Azure Data Catalog-portalen, men när du försöker logga in på registreringsverktyget, du får ett felmeddelande.
author: JasonWHowell
ms.author: jasonh
ms.service: data-catalog
ms.topic: conceptual
ms.date: 04/06/2019
ms.openlocfilehash: e69a7e3bd104d0fb82b248b6560d4fd082c88426
ms.sourcegitcommit: bf509e05e4b1dc5553b4483dfcc2221055fa80f2
ms.translationtype: HT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/22/2019
ms.locfileid: "59996449"
---
# <a name="azure-active-directory-policy-configuration"></a>Konfiguration av Azure Active Directory-principer

Det kan uppstå en situation då du kan logga in på Azure Data Catalog-portalen, men då du får ett felmeddelande när du försöker logga in till registreringsverktyget för datakällan. Det här felet kan uppstå när du är ansluten till företagets nätverk eller när du ansluter från utanför företagets nätverk.

## <a name="registration-tool"></a>Registreringsverktyget

Registreringsverktyget använder *formulärautentisering* för att validera användarinloggningar mot Azure Active Directory. För att inloggningen ska lyckas måste en Azure Active Directory-administratör aktivera formulärautentisering i *den globala autentiseringsprincipen*.

Med den globala autentiseringsprincipen kan du aktivera autentisering separat för intranät- och extranätsanslutningar, som du ser i följande bild. Inloggningsfel kan uppstå om formulärautentisering inte är aktiverat för det nätverk som du ansluter.

 ![Global Azure Active Directory-autentiseringsprincip](./media/troubleshoot-policy-configuration/global-auth-policy.png)

Mer information finns i [Konfigurera autentiseringsprinciper](/previous-versions/windows/it-pro/windows-server-2012-R2-and-2012/dn486781(v=ws.11)).

## <a name="next-steps"></a>Nästa steg

* [Skapa en Azure Data Catalog](data-catalog-get-started.md)