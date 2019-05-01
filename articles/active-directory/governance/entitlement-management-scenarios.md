---
title: Vanliga scenarier i Azure AD-rättigheten hantering (förhandsversion) – Azure Active Directory
description: Lär dig de övergripande stegen som du bör följa för vanliga scenarier i Azure Active Directory rättigheten hantering (förhandsversion).
services: active-directory
documentationCenter: ''
author: rolyon
manager: mtillman
editor: markwahl-msft
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.subservice: compliance
ms.date: 04/23/2019
ms.author: rolyon
ms.reviewer: mwahl
ms.collection: M365-identity-device-management
ms.openlocfilehash: 96442a9d49581da6841fa7acb8329354ec727f60
ms.sourcegitcommit: 2028fc790f1d265dc96cf12d1ee9f1437955ad87
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/30/2019
ms.locfileid: "64918482"
---
# <a name="common-scenarios-in-azure-ad-entitlement-management-preview"></a>Vanliga scenarier i Azure AD rättigheten hantering (förhandsversion)

> [!IMPORTANT]
> Azure Active Directory (Azure AD) rättigheten management är för närvarande i offentlig förhandsversion.
> Den här förhandsversionen tillhandahålls utan serviceavtal och rekommenderas inte för produktionsarbetsbelastningar. Vissa funktioner kanske inte stöds eller kan vara begränsade.
> Mer information finns i [Kompletterande villkor för användning av Microsoft Azure-förhandsversioner](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

Det finns flera sätt att du kan konfigurera hantering av rätt för din organisation. Om du precis har kommit igång, är det dock vara bra att förstå de vanliga scenarierna för administratörer, godkännare och begärande.

## <a name="administrators"></a>Administratörer

### <a name="im-new-to-entitlement-management-and-i-want-help-with-getting-started"></a>Jag har använt rätt hantering och jag vill ha hjälp med att komma igång

> [!div class="mx-tableFixed"]
> | Steg | Exempel |
> | --- | --- |
> | [Kursen för att skapa ditt första åtkomst-paket](entitlement-management-access-package-first.md) | [![Azure portal ikon](./media/entitlement-management-scenarios/azure-portal.png)](./media/entitlement-management-scenarios/azure-portal-expanded.png#lightbox) |

### <a name="i-want-to-allow-users-in-my-directory-to-request-access-to-groups-applications-or-sharepoint-sites"></a>Jag vill att användare i min katalog att begära åtkomst till grupper, program eller SharePoint-webbplatser

> [!div class="mx-tableFixed"]
> | Steg | Exempel |
> | --- | --- |
> | **1.** [Skapa ett nytt åtkomst-paket i en katalog](entitlement-management-access-package-create.md#start-new-access-package) | ![Skapa en access-paket](./media/entitlement-management-scenarios/access-package.png) |
> | **2.** [Lägg till resursroller för att komma åt paketet](entitlement-management-access-package-edit.md#add-resource-roles)<ul><li>Grupper</li><li>Appar</li><li>SharePoint-webbplatser</li></ul> | ![Lägg till resursroller](./media/entitlement-management-scenarios/resource-roles.png) |
> | **3.** [Lägg till en princip](entitlement-management-access-package-edit.md#policy-for-users-in-your-directory)<ul><li>För användare i din katalog</li><li>Godkännande krävs</li><li>Förfalloinställningar</li></ul> | ![Lägg till princip](./media/entitlement-management-scenarios/policy.png) |

### <a name="i-want-to-allow-users-from-my-business-partners-including-users-not-yet-in-my-directory-to-request-access-to-groups-applications-or-sharepoint-sites"></a>Jag vill att användare från min affärspartner (inklusive användare har ännu inte i min katalog) för att begära åtkomst till grupper, program eller SharePoint-webbplatser

> [!div class="mx-tableFixed"]
> | Steg | Exempel |
> | --- | --- |
> | **1.** [Skapa ett nytt åtkomst-paket i en katalog](entitlement-management-access-package-create.md#start-new-access-package) | ![Skapa en access-paket](./media/entitlement-management-scenarios/access-package.png) |
> | **2.** [Lägg till resursroller för att komma åt paketet](entitlement-management-access-package-edit.md#add-resource-roles) | ![Lägg till resursroller](./media/entitlement-management-scenarios/resource-roles.png) |
> | **3.** [Lägg till en princip för externa användare](entitlement-management-access-package-edit.md#policy-for-users-not-in-your-directory)<ul><li>För användare inte i din katalog</li><li>Godkännande krävs</li><li>Förfalloinställningar</li></ul> | ![Lägg till princip för externa användare](./media/entitlement-management-scenarios/policy-external.png) |
> | **4.** [Skicka mina åtkomst portal länken att begära åtkomst paketet till din affärspartner](entitlement-management-access-package-edit.md#copy-my-access-portal-link)<ul><li>Affärspartner kan dela länken med sina användare</li></ul> |  |

### <a name="i-want-to-change-the-groups-applications-or-sharepoint-sites-in-an-access-package"></a>Jag vill ändra grupper, program eller SharePoint-webbplatser i en åtkomst-paket

> [!div class="mx-tableFixed"]
> | Steg | Exempel |
> | --- | --- |
> | **1.** Öppna paketet åtkomst | ![Lägg till resursroller](./media/entitlement-management-scenarios/resource-roles.png) |
> | **2.** [Lägg till eller ta bort resursroller](entitlement-management-access-package-edit.md#add-resource-roles) | ![Lägg till resursroller](./media/entitlement-management-scenarios/resource-roles-add.png) |

### <a name="i-want-to-view-who-has-an-assignment-to-groups-applications-or-sharepoint-sites"></a>Jag vill visa vem som har en tilldelning till grupper, program eller SharePoint-webbplatser

> [!div class="mx-tableFixed"]
> | Steg | Exempel |
> | --- | --- |
> | **1.** Öppna ett åtkomst-paket | ![Lägg till resursroller](./media/entitlement-management-scenarios/resource-roles.png) |
> | **2.** [Visa tilldelningar](entitlement-management-access-package-edit.md#view-who-has-an-assignment)<ul><li>Visa vilka användare som har åtkomst till ett åtkomst-paket</li><li>Visa vilka användaråtkomst har upphört att gälla</li></ul> |  |

### <a name="i-want-to-view-groups-applications-or-sharepoint-sites-a-user-has-access-to"></a>Jag vill visa grupper, program eller en användare har åtkomst till SharePoint-webbplatser

> [!div class="mx-tableFixed"]
> | Steg | Exempel |
> | --- | --- |
> | [Visa rapport tilldelningar](entitlement-management-reports.md)<ul><li>Visa när de efterfrågas och som har godkänts</li></ul> |  |

## <a name="approvers"></a>Godkännare

### <a name="i-want-to-approve-requests-to-access-groups-applications-or-sharepoint-sites"></a>Jag vill godkänna förfrågningar om åtkomst till grupper, program eller SharePoint-webbplatser

> [!div class="mx-tableFixed"]
> | Steg | Exempel |
> | --- | --- |
> | **1.** [Öppna begäran i min Access portal](entitlement-management-request-approve.md#open-request) | [![Min portal ikonen åtkomst](./media/entitlement-management-scenarios/my-access-portal.png)](./media/entitlement-management-scenarios/my-access-portal-expanded.png#lightbox) |
> | **2.** [Godkänn åtkomstbegäran om](entitlement-management-request-approve.md#approve-or-deny-request) | ![Bevilja åtkomst](./media/entitlement-management-scenarios/approve-access.png) |

## <a name="requestors"></a>Begärande

### <a name="i-want-to-view-the-groups-applications-or-sharepoint-sites-available-to-me-and-request-access"></a>Jag vill visa grupper, program och SharePoint-webbplatser som är tillgängliga för mig och begära åtkomst

> [!div class="mx-tableFixed"]
> | Steg | Exempel |
> | --- | --- |
> | **1.** [Logga in på portalen Mina åtkomst](entitlement-management-request-access.md#sign-in-to-the-my-access-portal) | [![Min portal ikonen åtkomst](./media/entitlement-management-scenarios/my-access-portal.png)](./media/entitlement-management-scenarios/my-access-portal-expanded.png#lightbox) |
> | **2.** Hitta åtkomst paketet |  |
> | **3.** [Begär åtkomst](entitlement-management-request-access.md#request-an-access-package) | ![Begär åtkomst](./media/entitlement-management-scenarios/request-access.png) |

### <a name="im-an-external-user-and-i-want-to-request-access-to-groups-applications-or-sharepoint-sites-with-a-direct-link"></a>Jag har en extern användare och jag vill begära åtkomst till grupper, program eller SharePoint-webbplatser med en direktlänk

> [!div class="mx-tableFixed"]
> | Steg | Exempel |
> | --- | --- |
> | **1.** [Hitta Mina åtkomst portal länken du fick](entitlement-management-access-package-edit.md#copy-my-access-portal-link) |  |
> | **2.** [Logga in på portalen Mina åtkomst](entitlement-management-request-access.md#sign-in-to-the-my-access-portal) | [![Min portal ikonen åtkomst](./media/entitlement-management-scenarios/my-access-portal.png)](./media/entitlement-management-scenarios/my-access-portal-expanded.png#lightbox) |
> | **3.** [Begär åtkomst](entitlement-management-request-access.md#request-an-access-package) | ![Begär åtkomst till externa användare](./media/entitlement-management-scenarios/request-access-external.png) |

### <a name="i-want-to-view-the-groups-applications-or-sharepoint-sites-i-already-have-access-to"></a>Jag vill visa grupper, program eller SharePoint-webbplatser jag redan har åtkomst till

> [!div class="mx-tableFixed"]
> | Steg | Exempel |
> | --- | --- |
> | **1.** [Logga in på portalen Mina åtkomst](entitlement-management-request-access.md#sign-in-to-the-my-access-portal) | [![Min portal ikonen åtkomst](./media/entitlement-management-scenarios/my-access-portal.png)](./media/entitlement-management-scenarios/my-access-portal-expanded.png#lightbox) |
> | **2.** Visa aktiva åtkomst paket |  |

## <a name="next-steps"></a>Nästa steg

- [Självstudie: Skapa ditt första åtkomst-paket](entitlement-management-access-package-first.md)
- [Redigera och hantera ett befintligt paket för åtkomst](entitlement-management-access-package-edit.md)
