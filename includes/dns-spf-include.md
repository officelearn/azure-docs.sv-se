---
author: vhorne
ms.service: dns
ms.topic: include
ms.date: 11/25/2018
ms.author: victorh
ms.openlocfilehash: 9cc650cea17acb8d89933c819c4ca60e2c459d1c
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/28/2020
ms.locfileid: "67187974"
---
SPF-poster (Sender Policy Framework) används för att ange vilka e-postservrar som kan skicka e-post för ett domännamn. Korrekt konfiguration av SPF-poster är viktigt för att förhindra att mottagarna markerar din e-post som skräppost.

DNS-rfC:erna introducerade ursprungligen en ny SPF-posttyp för att stödja det här scenariot. För att stödja äldre namnservrar tillät de också användning av TXT-posttypen för att ange SPF-poster. Denna tvetydighet ledde till förvirring, som löstes av [RFC 7208](http://tools.ietf.org/html/rfc7208#section-3.1). Det står att SPF-poster måste skapas med hjälp av TXT-posttypen. Det anges också att SPF-posttypen är inaktuell.

**SPF-poster stöds av Azure DNS och måste skapas med hjälp av TXT-posttypen.** Den föråldrade SPF-posttypen stöds inte. När du [importerar en DNS-zonfil](../articles/dns/dns-import-export.md)konverteras alla SPF-poster som använder SPF-posttypen till TXT-posttypen.
