<properties
    pageTitle="Überlegungen zum Entwurf der Azure Active Directory-Hybrid-Identität – Ermitteln der Anforderungen in Bezug auf das Content Management | Microsoft Azure"
    description="Sie erhalten einen Einblick, wie Sie die Content Management-Anforderungen für Ihr Unternehmen ermitteln. Wenn Benutzer ein eigenes Gerät verwenden, verfügen sie meist auch über unterschiedliche Anmeldeinformationen, die je nach genutzter Anwendung eingesetzt werden. Es ist wichtig zu unterscheiden, welche Inhalte mit persönlichen Anmeldeinformationen und welche Inhalte mit den Anmeldeinformationen des Unternehmens erstellt wurden. Ihre Identitätslösung sollte in der Lage sein, mit Clouddiensten zu interagieren, um für Endbenutzer eine nahtlose Erfahrung zu schaffen, und dabei den Datenschutz sicherzustellen und den Schutz vor Datenlecks zu erhöhen."
    documentationCenter=""
    services="active-directory"
    authors="yuridio"
    manager="stevenpo"
    editor=""/>

<tags
    ms.service="active-directory"
    ms.devlang="na"
    ms.topic="article"
    ms.tgt_pltfrm="na"
    ms.workload="identity" 
    ms.date="11/11/2015"
    ms.author="yuridio"/>

# Ermitteln der Content Management-Anforderungen für Ihre Hybrid-Identitätslösung

Wenn Sie die Content Management-Anforderungen Ihres Unternehmens genau kennen, kann sich dies direkt auf die Entscheidung auswirken, welche Hybrid-Identitätslösung verwendet werden soll. Mit der Verbreitung von mehreren Geräten und die Möglichkeit von Benutzern, ihre eigenen Geräte zu bringen ([BYOD](http://aka.ms/byodcg)), das Unternehmen muss seine eigenen Daten schützen, aber es auch muss Datenschutz unverändert beibehalten. Wenn Benutzer ein eigenes Gerät verwenden, verfügen sie meist auch über unterschiedliche Anmeldeinformationen, die je nach genutzter Anwendung eingesetzt werden. Es ist wichtig zu unterscheiden, welche Inhalte mit persönlichen Anmeldeinformationen und welche Inhalte mit den Anmeldeinformationen des Unternehmens erstellt wurden. Ihre Identitätslösung sollte in der Lage sein, mit Clouddiensten zu interagieren, um für Endbenutzer eine nahtlose Erfahrung zu schaffen, und dabei den Datenschutz sicherzustellen und den Schutz vor Datenlecks zu erhöhen. 

Ihre Identitätslösung wird von unterschiedlichen technischen Steuerungen genutzt, um Content Management bereitzustellen. Dies ist in der folgenden Abbildung dargestellt:
 
![](./media/hybrid-id-design-considerations/securitycontrols.png)

**Sicherheitskontrollen, für die Ihr Identitätsverwaltungssystem verwendet wird**

Im Allgemeinen wird Ihr Identitätsverwaltungssystem in den folgenden Bereichen für Content Management-Anforderungen genutzt:

- Datenschutz: Identifizieren des Benutzers, dem eine Ressource gehört, und Anwenden der richtigen Kontrollen, um die Integrität zu wahren.
- Klassifizierung von Daten: Identifizieren des Benutzers bzw. der Gruppe und der Zugriffsebene für ein Objekt nach seiner Klassifizierung. 
- Schutz vor Datenlecks: Sicherheitskontrollen, die als Schutz vor Datenlecks dienen, müssen mit dem Identitätssystem interagieren, um die Identität des Benutzers zu überprüfen. Dies ist auch für Überwachungspfadzwecke wichtig.

>[AZURE.NOTE]
Lesen [Klassifizierung von Daten für die Cloud bereit](http://download.microsoft.com/download/0/A/3/0A3BE969-85C5-4DD2-83B6-366AA71D1FE3/Data-Classification-for-Cloud-Readiness.pdf) für Weitere Informationen zu best Practices und Richtlinien für die Klassifizierung von Daten.

Stellen Sie beim Planen Ihrer Hybrid-Identitätslösung sicher, dass die folgenden Fragen gemäß den Anforderungen Ihres Unternehmens beantwortet werden:

- Verfügt Ihr Unternehmen über Sicherheitskontrollen zur Durchsetzung des Datenschutzes?
 - Wenn ja: Können diese Sicherheitskontrollen in die Hybrid-Identitätslösung integriert werden, die Sie einführen möchten?
- Wird in Ihrem Unternehmen die Datenklassifizierung verwendet?
 - Wenn ja: Kann die aktuelle Lösung in die Hybrid-Identitätslösung integriert werden, die Sie einführen möchten?
- Verfügt Ihr Unternehmen derzeit über eine Lösung als Schutz vor Datenlecks? 
 - Wenn ja: Kann die aktuelle Lösung in die Hybrid-Identitätslösung integriert werden, die Sie einführen möchten?
- Muss Ihr Unternehmen den Zugriff auf Ressourcen überwachen?
 - Wenn ja: Für welche Art von Ressourcen gilt dies?
 - Wenn ja: Welche Informationsebene ist hierbei erforderlich?
 - Wenn ja: Wo muss sich das Überwachungsprotokoll befinden? Lokal oder in der Cloud?
- Muss Ihr Unternehmen E-Mails verschlüsseln, die vertrauliche Daten enthalten (Sozialversicherungsnummern, Kreditkartennummern usw.)?
- Muss Ihr Unternehmen alle Dokumente/Inhalte verschlüsseln, die mit externen Geschäftspartnern ausgetauscht werden?
- Muss Ihr Unternehmen für bestimmte Arten von E-Mails (Nicht beantworten, Nicht weiterleiten) Unternehmensrichtlinien durchsetzen?
 
>[AZURE.NOTE]
Notieren Sie sich alle Antworten, und stellen Sie sicher, dass Ihnen die Begründung der Antwort jeweils klar ist. [Definieren der Strategie für den Datenschutz](active-directory-hybrid-identity-design-considerations-data-protection-strategy.md) geht über die verfügbaren Optionen und die vor-und Nachteile der einzelnen Optionen.  Indem Sie diese Fragen beantworten, wählen Sie aus, welche Option Ihre Geschäftsanforderungen am besten erfüllt.


## Nächste Schritte
[Bestimmen der Zugriffssteuerungsanforderungen](active-directory-hybrid-identity-design-considerations-accesscontrol-requirements.md)

## Siehe auch
[Überlegungen zum Entwurf – Übersicht](active-directory-hybrid-identity-design-considerations-overview.md)


