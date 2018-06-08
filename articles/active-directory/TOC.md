# [Dokumentation om Azure Active Directory](index.md)

# Översikt
## [Vad är Azure Active Directory?](active-directory-whatis.md)
## [Om Azures identitetshantering](identity-fundamentals.md)
## [Förstå Azure-identitetslösningar](understand-azure-identity-solutions.md)
## [Välj en hybrididentitetslösning](choose-hybrid-identity-solution.md)
## [Associera Azure-prenumerationer](active-directory-how-subscriptions-associated-directory.md)
## [Överväganden för lagring och data](active-directory-data-storage-eu.md)
## [Vanliga frågor och svar](active-directory-faq.md)
## [Nyheter](whats-new.md)


# Kom igång
## [Kom igång med Azure AD](get-started-azure-ad.md)
## [Registrera dig för Azure AD Premium](active-directory-get-started-premium.md)
## [Lägga till ett anpassat domännamn](add-custom-domain.md)
## [Konfigurera varumärkesexponering](customize-branding.md)
## [Lägga till användare i Azure AD](add-users-azure-active-directory.md)
## [Tilldela licenser till användare](license-users-groups.md)
## [Konfigurera lösenordsåterställning via självbetjäning](authentication/quickstart-sspr.md)
## [Lägg till din organisations sekretessinformation i Azure AD](active-directory-properties-area.md)


# Gör så här för att
## Planera och designa
### [Förstå Azure AD-arkitektur](active-directory-architecture.md)
### [Anspråksmappning i Azure Active Directory](active-directory-claims-mapping.md)
### [Distribuera en hybrididentitetslösning](active-directory-hybrid-identity-design-considerations-overview.md)
#### Fastställa krav
##### [Identitet](active-directory-hybrid-identity-design-considerations-business-needs.md)
##### [Katalogsynkronisering](active-directory-hybrid-identity-design-considerations-directory-sync-requirements.md)
##### [Flerfaktorautentisering](active-directory-hybrid-identity-design-considerations-multifactor-auth-requirements.md)
##### [Strategi för identitetslivscykel](active-directory-hybrid-identity-design-considerations-lifecycle-adoption-strategy.md)
#### [Planera för datasäkerhet](active-directory-hybrid-identity-design-considerations-data-protection-strategy.md)
##### [Dataskydd](active-directory-hybrid-identity-design-considerations-dataprotection-requirements.md)
##### [Innehållshantering](active-directory-hybrid-identity-design-considerations-contentmgt-requirements.md)
##### [Åtkomstkontroll](active-directory-hybrid-identity-design-considerations-accesscontrol-requirements.md)
##### [Incidenthantering](active-directory-hybrid-identity-design-considerations-incident-response-requirements.md)
#### Planera din identitetslivscykel
##### [Uppgifter](active-directory-hybrid-identity-design-considerations-hybrid-id-management-tasks.md)
##### [Införandestrategi](active-directory-hybrid-identity-design-considerations-identity-adoption-strategy.md)
#### [Nästa steg](active-directory-hybrid-identity-design-considerations-nextsteps.md)
#### [Jämförelse av verktyg](active-directory-hybrid-identity-design-considerations-tools-comparison.md)

## Hantera användare
### [Lägga till nya användare i Azure AD](add-users-azure-active-directory.md)
### [Hantera användarprofiler](active-directory-users-profile-azure-portal.md)
### [Dela konton](active-directory-sharing-accounts.md)
### [Tilldela användare administrativa roller](active-directory-users-assign-role-azure-portal.md)
### [Återställa en borttagen användare](active-directory-users-restore.md)
### [Lägga till gästanvändare från annan katalog (B2B)](b2b/what-is-b2b.md)
#### [Administratörer som lägger till B2B-användare](b2b/add-users-administrator.md)
#### [Informationsarbetare som lägger till B2B-användare](b2b/add-users-information-worker.md)
#### [API och anpassning](b2b/customize-invitation-api.md)
#### [Kod och Azure PowerShell-exempel](b2b/code-samples.md)
#### [Exempel på registreringsportal för självbetjäning](b2b/self-service-portal.md)
#### [E-postinbjudan](b2b/invitation-email-elements.md)
#### [Inlösning av inbjudan](b2b/redemption-experience.md)
#### [Lägga till B2B-användare utan inbjudan](b2b/add-user-without-invite.md)
#### [Tillåta eller blockera inbjudningar](b2b/allow-deny-list.md)
#### [Villkorlig åtkomst för B2B](b2b/conditional-access.md)
#### [B2B-delningsprinciper](b2b/delegate-invitations.md)
#### [Lägg till en B2B-användare till en roll](b2b/add-guest-to-role.md)
#### [Dynamiska grupper och B2B-användare](b2b/use-dynamic-groups.md)
#### [Lämna en organisation](b2b/leave-the-organization.md)
#### [Granskning och rapporter](b2b/auditing-and-reporting.md)
#### [B2B för hybridorganisationer](b2b/hybrid-organizations.md)
##### [Bevilja B2B-användare åtkomst till lokala appar](b2b/hybrid-cloud-to-on-premises.md)
##### [Bevilja lokala användare åtkomst till molnappar](b2b/hybrid-on-premises-to-cloud.md)
#### [Extern delning av B2B och Office 365](b2b/o365-external-user.md)
#### [B2B-licensiering](b2b/licensing-guidance.md)
#### [Aktuella begränsningar](b2b/current-limitations.md)
#### [Vanliga frågor och svar](b2b/faq.md)
#### [Felsökning av B2B](b2b/troubleshoot.md)
#### [Förstå B2B-användaren](b2b/user-properties.md)
#### [Användartoken för B2B](b2b/user-token.md)
#### [B2B för Azure AD-integrerade appar](b2b/configure-saas-apps.md)
#### [Mappning av användaranspråk för B2B](b2b/claims-mapping.md)
#### [Jämför B2B-samarbete med B2C](b2b/compare-with-b2c.md)
#### [Få support för B2B](b2b/get-support.md)

## [Hantera grupper och medlemmar](active-directory-manage-groups.md)
### Hantera grupper
#### [Azure Portal](active-directory-groups-create-azure-portal.md)
#### [Azure AD PowerShell för Graph (v2)](active-directory-accessmanagement-groups-settings-v2-cmdlets.md)
#### [Azure AD PowerShell MSOnline](active-directory-accessmanagement-groups-settings-cmdlets.md)
### [Hantera gruppmedlemmar](active-directory-groups-members-azure-portal.md)
### [Hantera gruppägare](active-directory-accessmanagement-managing-group-owners.md)
### [Hantera gruppmedlemskap](active-directory-groups-membership-azure-portal.md)
### [Tilldela licenser med hjälp av grupper](active-directory-licensing-whatis-azure-portal.md)
#### [Tilldela licenser till en grupp](active-directory-licensing-group-assignment-azure-portal.md)
#### [Identifiera och åtgärda licensproblem i en grupp](active-directory-licensing-group-problem-resolution-azure-portal.md)
#### [Migrera enskilda licensierade användare till gruppbaserad licensiering](active-directory-licensing-group-migration-azure-portal.md)
#### [Migrera användare mellan produktlicenser](active-directory-licensing-group-product-migration.md)
#### [Fler scenarier för gruppbaserad licensiering](active-directory-licensing-group-advanced.md)
#### [Azure PowerShell-exempel för gruppbaserad licensiering](active-directory-licensing-ps-examples.md)
#### [Referens för produkter och tjänstplaner i Azure AD](active-directory-licensing-product-and-service-plan-reference.md)
### [Konfigurera förfallodatum för Office 365-grupper](active-directory-groups-lifecycle-azure-portal.md)
### [Framtvinga en namnprincip för grupper](groups-naming-policy.md)
### [Visa alla grupper](active-directory-groups-view-azure-portal.md)
### [Hantera gruppåtkomst till SaaS-appar](active-directory-accessmanagement-group-saasapps.md)
### [Återställa en borttagen Office 365-grupp](active-directory-groups-restore-azure-portal.md)
### [Hantera gruppinställningar](active-directory-groups-settings-azure-portal.md) 
### Skapa avancerade regler
#### [Azure Portal](active-directory-groups-dynamic-membership-azure-portal.md)
### [Konfigurera självbetjäningsgrupper](active-directory-accessmanagement-self-service-group-management.md)
### [Felsöka](active-directory-accessmanagement-troubleshooting.md)

## [Hantera rapporter](active-directory-reporting-azure-portal.md)
### [Inloggningsaktiviteter](active-directory-reporting-activity-sign-ins.md)
### [Granska aktivitet](active-directory-reporting-activity-audit-logs.md)
### [Användare i riskzonen](active-directory-reporting-security-user-at-risk.md)
### [Riskfyllda inloggningar](active-directory-reporting-security-risky-sign-ins.md)
### [Riskhändelser](active-directory-reporting-risk-events.md)
### [Vanliga frågor och svar](active-directory-reporting-faq.md)
### Uppgifter
#### [Konfigurera namngivna platser](active-directory-named-locations.md)
#### [Hitta aktivitetsrapporter](active-directory-reporting-migration.md)
#### [Använd innehållspaketet för Azure Active Directory Power BI](active-directory-reporting-power-bi-content-pack-how-to.md)
#### [Åtgärda användare som har flaggats för risk](active-directory-report-security-user-at-risk-remediation.md)
### Referens
#### [Kvarhållning](active-directory-reporting-retention.md)
#### [Svarstider](active-directory-reporting-latencies-azure-portal.md)
#### [Meddelanden](active-directory-reporting-notifications.md)
#### [Granska aktivitetsreferens](active-directory-reporting-activity-audit-reference.md)
#### [Felkoder för inloggningsaktivitet](active-directory-reporting-activity-sign-ins-errors.md)
#### [Multi-factor authentication](active-directory-reporting-activity-sign-ins-mfa.md



### Felsöka
#### [Granskningsdata som saknas](active-directory-reporting-troubleshoot-missing-audit-data.md)
#### [Data som saknas i nedladdningar](active-directory-reporting-troubleshoot-missing-data-download.md)
#### [Fel i innehållspaket för Azure Active Directory-aktivitetsloggar](active-directory-reporting-troubleshoot-content-pack.md)
### [Programmässig åtkomst](active-directory-reporting-api-getting-started-azure-portal.md)
#### [Granska referens](active-directory-reporting-api-audit-reference.md)
#### [Inloggningsreferens](active-directory-reporting-api-sign-in-activity-reference.md)
#### [Förutsättningar](active-directory-reporting-api-prerequisites-azure-portal.md)
#### [Granska exempel](active-directory-reporting-api-audit-samples.md)
#### [Inloggningsexempel](active-directory-reporting-api-sign-in-activity-samples.md)
#### [Använda certifikat](active-directory-reporting-api-with-certificates.md)

## Hantera lösenord
### [Lösenordsöversikt](authentication/active-directory-passwords-overview.md)
### Användardokument
#### [Återställ eller ändra ditt lösenord](active-directory-passwords-update-your-own-password.md)
#### [Metodtips för lösenord](active-directory-secure-passwords.md)
#### [Registrera för återställning av lösenord för självbetjäning](active-directory-passwords-reset-register.md)
### [SSPR – så här fungerar det](authentication/concept-sspr-howitworks.md)
### [SSPR-distributionsguide](authentication/howto-sspr-deployment.md)
### [SSPR och Windows 10](authentication/tutorial-sspr-windows.md)
### [SSPR-principer ](authentication/concept-sspr-policy.md)
### [SSPR-anpassning](authentication/concept-sspr-customization.md)
### [SSPR-datakrav](authentication/howto-sspr-authenticationdata.md)
### [SSPR-rapportering](authentication/howto-sspr-reporting.md)
### IT-administratörer: Återställ lösenord
#### [Azure Portal](active-directory-users-reset-password-azure-portal.md)
### [Licensiera SSPR](authentication/concept-sspr-licensing.md)
### [Tillbakaskrivning av lösenord](authentication/howto-sspr-writeback.md)
### [Felsöka](authentication/active-directory-passwords-troubleshoot.md)
### [Vanliga frågor och svar](authentication/active-directory-passwords-faq.md)


## Hantera enheter
### [Introduktion](device-management-introduction.md)
### [Använd Azure Portal](device-management-azure-portal.md)
### [Planera Azure AD Join](active-directory-azureadjoin-deployment-aadjoindirect.md)
### [Vanliga frågor och svar](device-management-faq.md)
### Uppgifter
#### [Konfigurera Azure AD-registrerade Windows 10-enheter](device-management-azuread-registered-devices-windows10-setup.md)
#### [Konfigurera Azure AD-anslutna enheter](device-management-azuread-joined-devices-setup.md)
#### [Konfigurera Azure AD-anslutna hybrid-enheter](device-management-hybrid-azuread-joined-devices-setup.md)
#### [Distribuera lokalt](active-directory-device-registration-on-premises-setup.md)
#### [Azure AD-anslutning under första körningen av Windows 10](device-management-azuread-joined-devices-frx.md)
### Felsöka
#### [Azure AD-anslutna Windows 10- och Windows Server 2016-hybridenheter](device-management-troubleshoot-hybrid-join-windows-current.md)
#### [Tidigare Azure AD-anslutna Windows-hybridenheter ](device-management-troubleshoot-hybrid-join-windows-legacy.md)

## Hantera appar
### [Översikt](manage-apps/what-is-application-management.md)
### [Komma igång](manage-apps/plan-an-application-integration.md)
### [Självstudier om SaaS-appintegration](active-directory-saas-tutorial-list.md)
### [Cloud App Discovery](manage-apps/cloud-app-discovery.md)
#### [Skapa ögonblicksrapporter](manage-apps/cloud-app-discovery-create-snapshot-reports.md)
#### [Konfigurera kontinuerlig rapportering](https://docs.microsoft.com/cloud-app-security/discovery-docker)
#### [Använda en parser för loggar](https://docs.microsoft.com/cloud-app-security/custom-log-parser)

### [Användaretablering och -avetablering till SaaS-appar](active-directory-saas-app-provisioning.md) 
#### [Självstudier om appintegration](active-directory-saas-tutorial-list.md) 
#### [Automatisera etablering till SCIM-aktiverade appar](active-directory-scim-provisioning.md) 
#### [Anpassa attributmappningar](active-directory-saas-customizing-attribute-mappings.md) 
#### [Skriva uttryck för attributmappningar](active-directory-saas-writing-expressions-for-attribute-mappings.md) 
#### [Använda omfångsfilter](active-directory-saas-scoping-filters.md) 
#### [Rapportera om automatisk användaretablering](active-directory-saas-provisioning-reporting.md) 
#### [Felsöka användaretablering](active-directory-application-provisioning-content-map.md) 

### [Få åtkomst till appar med App Proxy](manage-apps/application-proxy.md)
#### Kom igång
##### [Aktivera App Proxy](manage-apps/application-proxy-enable.md)
##### [Publicera appar](manage-apps/application-proxy-publish-azure-portal.md)
##### [Anpassade domäner](manage-apps/application-proxy-configure-custom-domain.md)
#### [Enkel inloggning](manage-apps/application-proxy-single-sign-on.md)
##### [Enkel inloggning med KCD](manage-apps/application-proxy-configure-single-sign-on-with-kcd.md)
##### [Enkel inloggning med rubriker](manage-apps/application-proxy-configure-single-sign-on-with-ping-access.md)
##### [Enkel inloggning med lösenordsvalv](manage-apps/application-proxy-configure-single-sign-on-password-vaulting.md)
#### Begrepp
##### [Anslutningsappar](manage-apps/application-proxy-connectors.md)
##### [Säkerhet](manage-apps/application-proxy-security.md)
##### [Nätverk](manage-apps/application-proxy-network-topology.md)


##### [Uppgradera från TMG eller UAG](manage-apps/application-proxy-migration.md)

#### Avancerade konfigurationer
##### [Publicera i separata nätverk](manage-apps/application-proxy-connector-groups.md)
##### [Proxyservrar](manage-apps/application-proxy-configure-connectors-with-proxy-servers.md)
##### [Anspråksmedvetna appar](manage-apps/application-proxy-configure-for-claims-aware-applications.md)
##### [Ursprungliga klientappar](manage-apps/application-proxy-configure-native-client-application.md)
##### [Tyst installation](manage-apps/application-proxy-register-connector-powershell.md)
##### [Anpassad startsida](manage-apps/application-proxy-configure-custom-home-page.md)
##### [Översätt infogade länkar](manage-apps/application-proxy-configure-hard-coded-link-translation.md)
##### [Jokertecken](active-directory-application-proxy-wildcard.md)
##### [Ta bort personliga data](manage-apps/application-proxy-remove-personal-data.md)


#### Publicera genomgångar
##### [Fjärrskrivbord](manage-apps/application-proxy-integrate-with-remote-desktop-services.md)
##### [SharePoint](manage-apps/application-proxy-integrate-with-sharepoint-server.md)
##### [Microsoft Teams](application-proxy-teams.md)
##### [Tableau](active-directory-application-proxy-tableau.md)
##### [Qlik](active-directory-application-proxy-qlik.md)
#### [PowerShell](https://docs.microsoft.com/powershell/module/azuread/?view=azureadps-2.0#application_proxy_application_management) 

#### [Felsöka](active-directory-application-proxy-troubleshoot.md)

### Hantera företagets appar
#### [Tilldela användare](active-directory-coreapps-assign-user-azure-portal.md)
#### [Anpassa profilering](active-directory-coreapps-change-app-logo-user-azure-portal.md)
#### [Inaktivera användarinloggningar](active-directory-coreapps-disable-app-azure-portal.md)
#### [Ta bort användare](active-directory-coreapps-remove-assignment-azure-portal.md)
#### [Visa alla mina appar](active-directory-coreapps-view-azure-portal.md)
#### [Hantera användarens kontoetablering](active-directory-enterprise-apps-manage-provisioning.md)
#### [Hantera enkel inloggning för företagsappar](active-directory-enterprise-apps-manage-sso.md)
#### [Avancerad certifikatsignering för SAML-appar](active-directory-enterprise-apps-advance-certificate-options.md)
#### [Dölja ett program från en användarmiljö](active-directory-coreapps-hide-third-party-app.md)
### [Konfigurera automatisk acceleration för inloggning med hjälp av HRD-princip](active-directory-auto-acceleration-using-hrd.md)
### [Migrera AD FS-appar till Azure AD](migrate-adfs-apps-to-azure.md) 
### [Hantera åtkomst till appar](active-directory-managing-access-to-apps.md)
#### [Åtkomst med enkel inloggning](manage-apps/what-is-single-sign-on.md)
#### [Certifikat för enkel inloggning](active-directory-sso-certs.md)
#### [Klientrestriktioner](active-directory-tenant-restrictions.md)
#### [Använd SCIM-etablering av användare](active-directory-scim-provisioning.md)

### [Felsöka](active-directory-application-troubleshoot-content-map.md)
#### [Programutveckling](active-directory-application-dev-troubleshoot-content-map.md)
##### [Konfiguration och registrering](active-directory-application-dev-config-content-map.md)
##### [Utveckling](active-directory-application-dev-development-content-map.md)
#### [Programhantering](active-directory-application-management-troubleshoot-content-map.md)
##### [Konfiguration](active-directory-application-config-content-map.md)
##### [Inloggning](active-directory-application-sign-in-content-map.md)
##### [Etablering](active-directory-application-provisioning-content-map.md)

###### [Verifiering av en användare har etablerats](application-provisioning-when-will-provisioning-finish-specific-user.md) 
###### [Etableringen tar lång tid](application-provisioning-when-will-provisioning-finish.md) 
###### [Konfigurera användaretablering](application-provisioning-config-how-to.md) 
###### [Konfigurera etablering problem](application-provisioning-config-problem.md) 
###### [Problem med att spara autentiseringsuppgifter för administratörer](application-provisioning-config-problem-storage-limit.md) 
###### [Inga användare etableras](application-provisioning-config-problem-no-users-provisioned.md) 
###### [Fel användare etableras](application-provisioning-config-problem-wrong-users-provisioned.md) 

##### [Hantera åtkomst](active-directory-application-access-content-map.md)
##### [Åtkomstpanel](active-directory-application-access-panel-content-map.md)
##### [Programproxy](active-directory-application-proxy-content-map.md)
##### [Villkorlig åtkomst](active-directory-application-conditional-access-content-map.md)
### [Utveckla appar](active-directory-applications-guiding-developers-for-lob-applications.md)
### [Dokumentbibliotek](active-directory-apps-index.md)

## Hantera din katalog
### [Azure AD Connect](./connect/active-directory-aadconnect.md)
### Egna domännamn
#### [Snabbstart](add-custom-domain.md)
#### [Lägga till anpassade domännamn](active-directory-domains-manage-azure-portal.md)
### [Administrera din katalog](active-directory-administer.md)
### [Flera kataloger](active-directory-licensing-directory-independence.md)
### [Registrering av självbetjäning](active-directory-self-service-signup.md)
### [Ta över en ohanterad katalog](domains-admin-takeover.md)
### [Företagsroaming](active-directory-windows-enterprise-state-roaming-overview.md)
#### [Aktivera](active-directory-windows-enterprise-state-roaming-enable.md)
#### [Grupprincipinställningar](active-directory-windows-enterprise-state-roaming-group-policy-settings.md)
#### [Windows 10-inställningar](active-directory-windows-enterprise-state-roaming-windows-settings-reference.md)
#### [Vanliga frågor och svar](active-directory-windows-enterprise-state-roaming-faqs.md)
#### [Felsöka](active-directory-windows-enterprise-state-roaming-troubleshooting.md)


### [Integrera lokala identiteter med Azure AD Connect](./connect/active-directory-aadconnect.md)

## [Hantera åtkomst till Azure](../role-based-access-control/toc.yml)

## Delegera åtkomst till resurser
### [Administratörsroller](active-directory-assign-admin-roles-azure-portal.md)
#### [Tilldela administratörsroll till en användare](active-directory-users-assign-role-azure-portal.md) 
#### [Jämföra behörigheter för medlemmar och gästanvändare](users-default-permissions.md) 
### [Säker privilegierad åtkomst](admin-roles-best-practices.md)  
### [Skapa åtkomst till administratörskonton vid akutfall](active-directory-admin-manage-emergency-access-accounts.md) 


#### [Standardinställningar för användarbehörigheter](users-default-permissions.md)
### [Administrativa enheter](active-directory-administrative-units-management.md)
### [Konfigurera livslängd för token](active-directory-configurable-token-lifetimes.md)
### [Skydda privilegierade roller](admin-roles-best-practices.md)

## Åtkomstgranskningar
### [Översikt över åtkomstgranskningar](active-directory-azure-ad-controls-access-reviews-overview.md)
### [Slutför en åtkomstgranskning](active-directory-azure-ad-controls-complete-access-review.md)
### [Skapa en åtkomstgranskning](active-directory-azure-ad-controls-create-access-review.md)
### [Så här utför du en åtkomstgranskning](active-directory-azure-ad-controls-perform-access-review.md)
### [Så här granskar du din åtkomst](active-directory-azure-ad-controls-how-to-review-your-access.md)
### [Gäståtkomst med åtkomstgranskningar](active-directory-azure-ad-controls-manage-guest-access-with-access-reviews.md)
### [Hantera användaråtkomst med granskningar](active-directory-azure-ad-controls-manage-user-access-with-access-reviews.md)
### [Hantera program och kontroller](active-directory-azure-ad-controls-manage-programs-controls.md)
### [Hämta resultat för åtkomstgranskning](active-directory-azure-ad-controls-retrieve-access-review.md)

## Skydda dina identiteter
### [Villkorlig åtkomst](active-directory-conditional-access-azure-portal.md)
#### [Villkor](active-directory-conditional-access-conditions.md)
#### [Platsvillkor](active-directory-conditional-access-locations.md)
#### [Kontroller](active-directory-conditional-access-controls.md)
#### [Komma igång](active-directory-conditional-access-azure-portal-get-started.md)
#### [Bästa praxis](active-directory-conditional-access-best-practices.md)
#### [Förstå enhetsprinciper för Office 365-tjänster](active-directory-conditional-access-device-policies.md)
#### [Migrera klassiska principer](active-directory-conditional-access-migration.md)
#### [Konsekvensverktyg](active-directory-conditional-access-whatif.md)
#### Snabbstart
##### [Konfigurera MFA per app](active-directory-conditional-access-app-based-mfa.md)
#### Uppgifter
##### [Migrera en klassisk MFA-princip](active-directory-conditional-access-migration-mfa.md)
##### [Konfigurera enhetsbaserad villkorlig åtkomst](active-directory-conditional-access-policy-connected-applications.md)
##### [Konfigurera appbaserad villkorlig åtkomst](active-directory-conditional-access-mam.md)
##### [Ange användningsvillkor för användare och appar](active-directory-tou.md)
##### [Konfigurera VPN-anslutning](https://docs.microsoft.com/windows-server/remote/remote-access/vpn/always-on-vpn/deploy/always-on-vpn-deploy)
##### [Konfigurera SharePoint och Exchange Online](active-directory-conditional-access-no-modern-authentication.md)
##### [Reparation](active-directory-conditional-access-device-remediation.md)
#### [Teknisk referens](active-directory-conditional-access-technical-reference.md)
#### [Vanliga frågor och svar](active-directory-conditional-faqs.md)


### Certifikatbaserad autentisering
#### [Android](active-directory-certificate-based-authentication-android.md)
#### [iOS](active-directory-certificate-based-authentication-ios.md)
#### [Komma igång](active-directory-certificate-based-authentication-get-started.md)

### [Azure AD Identity Protection](active-directory-identityprotection.md)
#### [Aktivera](active-directory-identityprotection-enable.md)
#### [Hitta sårbarheter](active-directory-identityprotection-vulnerabilities.md)
#### [Riskhändelser](active-directory-identity-protection-risk-events.md)
#### [Meddelanden](active-directory-identityprotection-notifications.md)
#### [Inloggning](active-directory-identityprotection-flows.md)
#### [Simulera riskhändelser](active-directory-identityprotection-playbook.md)
#### [Avblockera användare](active-directory-identityprotection-unblock-howto.md)
#### [Vanliga frågor och svar](active-directory-identity-protection-faqs.md)
#### [Ordlista](active-directory-identityprotection-glossary.md)
#### [Microsoft Graph](active-directory-identityprotection-graph-getting-started.md)
### [Privileged Identity Management](active-directory-privileged-identity-management-configure.md)

## Integrera andra tjänster med Azure AD 
### [Integrera LinkedIn med Azure AD](linkedin-integration.md)

## [Distribuera AD FS i Azure](active-directory-aadconnect-azure-adfs.md)
### [Hög tillgänglighet](active-directory-adfs-in-azure-with-azure-traffic-manager.md)
### [Ändra signaturens hashalgoritm](active-directory-federation-sha256-guidance.md)

## [Felsöka](active-directory-troubleshooting-support-howto.md)

## Distribuera Azure AD PoC (Proof of Concept)
### [PoC-strategibok: Introduktion](active-directory-playbook-intro.md)
### [PoC-strategibok: Ingredienser](active-directory-playbook-ingredients.md)
### [PoC-strategibok: Implementering](active-directory-playbook-implementation.md)
### [PoC-strategibok: Komponenter](active-directory-playbook-building-blocks.md)


# Referens
## [Kodexempel](https://azure.microsoft.com/resources/samples/?service=active-directory)
## [Azure PowerShell-cmdletar](/powershell/azure/overview)
## [Java API-referens](/java/api)
## [.NET-API](/active-directory/adal/microsoft.identitymodel.clients.activedirectory)
## [Tjänstens begränsningar](active-directory-service-limits-restrictions.md)

# Relaterat
## [Multi-Factor Authentication](/azure/multi-factor-authentication/)
## [Azure AD Connect](./connect/active-directory-aadconnect.md)
## [Azure AD Connect Health](./connect-health/active-directory-aadconnect-health.md)
## [Azure AD för utvecklare](./develop/active-directory-how-to-integrate.md)
## [Azure AD Privileged Identity Management](./privileged-identity-management/active-directory-securing-privileged-access.md)

# Resurser
## [Azures feedbackforum](https://feedback.azure.com/forums/169401-azure-active-directory)
## [Azure-översikt](https://azure.microsoft.com/roadmap/?category=security-identity)
## [MSDN-forum](https://social.msdn.microsoft.com/Forums/azure/en-US/home?forum=WindowsAzureAD)
## [Prissättning](https://azure.microsoft.com/pricing/details/active-directory/)
## [Priskalkylator](https://azure.microsoft.com/pricing/calculator/)
## [Tjänstuppdateringar](https://azure.microsoft.com/updates/?product=active-directory)
## [Stack Overflow](http://stackoverflow.com/questions/tagged/azure-active-directory)
## [Videoklipp](https://azure.microsoft.com/documentation/videos/index/?services=active-directory)
