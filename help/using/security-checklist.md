---
title: Die Dispatcher-Sicherheitscheckliste
description: Eine Sicherheitscheckliste, die vor der Produktion geprüft werden sollte.
contentOwner: User
products: SG_EXPERIENCEMANAGER/DISPATCHER
topic-tags: dispatcher
content-type: reference
jcr-lastmodifiedby: remove-legacypath-6-1
index: y
internal: n
snippet: y
exl-id: 49009810-b5bf-41fd-b544-19dd0c06b013
source-git-commit: 2d90738d01fef6e37a2c25784ed4d1338c037c23
workflow-type: tm+mt
source-wordcount: '591'
ht-degree: 56%

---

# Die Dispatcher-Sicherheitscheckliste{#the-dispatcher-security-checklist}

<!-- 

Comment Type: remark
Last Modified By: unknown unknown (ims-author-00AF43764F54BE740A490D44@AdobeID)
Last Modified Date: 2015-06-05T05:14:35.365-0400

<p>Food for thought listed on <a href="https://jira.corp.adobe.com/browse/DOC-5649">DOC-5649</a>. To be considered while proof-reading.</p> 
<p> </p>

 -->

Adobe empfiehlt, die folgende Checkliste auszufüllen, bevor Sie mit der Produktion beginnen.

>[!CAUTION]
>
>Füllen Sie vor der Live-Schaltung die Sicherheitscheckliste Ihrer AEM aus. Siehe die entsprechenden [Adobe Experience Manager-Dokumentation](https://experienceleague.adobe.com/en/docs/experience-manager-65/content/security/security-checklist).

## Verwenden der neuesten Version des Dispatchers {#use-the-latest-version-of-dispatcher}

Installieren Sie die neueste Version, die für Ihre Plattform verfügbar ist. Stellen Sie sicher, dass Sie Ihre Dispatcher-Instanz aktualisieren, sodass Sie die neueste Version verwenden, um von den Verbesserungen an Produkten und Sicherheit zu profitieren. Weitere Informationen finden Sie unter [Installieren des Dispatchers](dispatcher-install.md).

>[!NOTE]
>
>Überprüfen Sie die aktuelle Version Ihrer Dispatcher-Installation, indem Sie sich die Dispatcher-Protokolldatei ansehen.
>
>`[Thu Apr 30 17:30:49 2015] [I] [23171(140735307338496)] Dispatcher initialized (build 4.1.9)`
>
>Um die Protokolldatei zu finden, überprüfen Sie die Dispatcher-Konfiguration in Ihrem `httpd.conf`.

## Einschränken von Clients, die den Cache leeren können {#restrict-clients-that-can-flush-your-cache}

Adobe empfiehlt, [die Clients einzuschränken, die den Cache leeren können](dispatcher-configuration.md#limiting-the-clients-that-can-flush-the-cache).

## Aktivieren von HTTPS für Transport Layer Security {#enable-https-for-transport-layer-security}

Adobe empfiehlt die Aktivierung der HTTPS-Transportschicht sowohl auf der Autoren- als auch auf der Veröffentlichungsinstanz.

<!-- 

Comment Type: remark
Last Modified By: unknown unknown (ims-author-00AF43764F54BE740A490D44@AdobeID)
Last Modified Date: 2015-06-26T04:41:28.841-0400

<p>Recommended to have SSL termination, front end SSL.</p> 
<p>Question is do we want to have SSL communication between dispatcher and AEM instances (publish and/or author).</p> 
<p>We might want to have two items:</p> 
<ul> 
 <li>MUST HTTPS clients -&gt; dispatcher / load balancer</li> 
 <li>NICE load balancer -&gt; dispatcher<br /> </li> 
 <li>NICE dispatcher -&gt; instances if sensitive information such as credit cards / or infrastructure requirements such as DMZ</li> 
</ul>

 -->

## Einschränken des Zugriffs {#restrict-access}

Schränken Sie beim Konfigurieren des Dispatchers den externen Zugriff so weit wie möglich ein. Siehe das [Beispiel für einen Abschnitt /filter](dispatcher-configuration.md#main-pars_184_1_title) in der Dispatcher-Dokumentation.

## Verweigern des Zugriffs auf administrative URLs  {#make-sure-access-to-administrative-urls-is-denied}

Stellen Sie sicher, dass Sie Filter verwenden, um den externen Zugriff auf sämtliche administrative URLs wie die Web-Konsole zu blockieren.

Siehe [Testen der Dispatcher-Sicherheit](dispatcher-configuration.md#testing-dispatcher-security) für eine Liste von URLs, die blockiert werden müssen.

## Verwenden von Zulassungslisten anstelle von Blockierungslisten {#use-allowlists-instead-of-blocklists}

Zulassungslisten eignen sich besser zur Zugriffskontrolle, da sie grundsätzlich voraussetzen, dass alle Zugriffsanfragen verweigert werden sollten, sofern sie nicht ausdrücklich Teil der Zulassungsliste sind. Dieses Modell bietet eine restriktivere Kontrolle über neue Anforderungen, die während einer bestimmten Konfigurationsphase möglicherweise noch nicht überprüft oder in Betracht gezogen wurden.

## Ausführen des Dispatchers mit einer dedizierten Systembenutzerin bzw. einem dedizierten Systembenutzer {#run-dispatcher-with-a-dedicated-system-user}

Beim Konfigurieren des Dispatchers sollten Sie sicherstellen, dass der Webserver von einem dedizierten Benutzer mit den geringsten Berechtigungen ausgeführt wird. Es wird empfohlen, nur Schreibzugriff auf den Dispatcher-Cache-Ordner zu gewähren.

Außerdem müssen IIS-Benutzer ihre Website wie folgt konfigurieren:

1. Wählen Sie in den Einstellungen für den physischen Pfad für Ihre Website die Option zum **Verbinden als bestimmter Benutzer** aus.
1. Legen Sie die Person fest.

## Vermeiden von Denial-of-Service-Angriffen  {#prevent-denial-of-service-dos-attacks}

Ein Denial-of-Service-Angriff (DoS) zielt darauf ab, eine Computerressource für die vorgesehenen Personen unzugänglich zu machen.

Auf Dispatcher-Ebene gibt es [zwei Methoden zur Konfiguration zur Vermeidung von DoS-Angriffen](https://experienceleaguecommunities.adobe.com/t5/adobe-experience-manager/configure-aem-dispatcher-to-prevent-dos-attacks-aem-community/m-p/447780).

* Verwenden Sie das mod_rewrite-Modul (beispielsweise [Apache 2.4](https://httpd.apache.org/docs/2.4/mod/mod_rewrite.html), um URL-Überprüfungen durchzuführen (sofern die Regeln für das URL-Muster nicht zu komplex sind).

* Verhindert, dass der Dispatcher URLs mit falschen Erweiterungen zwischenspeichert, indem er [Filter](dispatcher-configuration.md#configuring-access-to-conten-tfilter).\
  Ändern Sie beispielsweise die Cachingregeln, um die Zwischenspeicherung auf die erwarteten MIME-Typen zu begrenzen. Dazu zählen unter anderem:

   * `.html`
   * `.jpg`
   * `.gif`
   * `.swf`
   * `.js`
   * `.doc`
   * `.pdf`
   * `.ppt`

  Sie können eine Beispielkonfigurationsdatei zum [Einschränken des externen Zugriffs](#restrict-access) einsehen, diese beinhaltet die Einschränkungen für MIME-Typen. 

Um sicher den vollen Funktionsumfang für die Veröffentlichungsinstanzen zu aktivieren, konfigurieren Sie Filter, um den Zugriff auf die folgenden Knoten zu verhindern:

* `/etc/`
* `/libs/`

Konfigurieren Sie anschließend Filter, um den Zugriff auf die folgenden Knotenpfade zuzulassen:

* `/etc/designs/*`
* `/etc/clientlibs/*`
* `/etc/segmentation.segment.js`
* `/libs/cq/personalization/components/clickstreamcloud/content/config.json`
* `/libs/wcm/stats/tracker.js`
* `/libs/cq/personalization/*` (JS, CSS und JSON)
* `/libs/cq/security/userinfo.json` (CQ-Benutzerinformationen)
* `/libs/granite/security/currentuser.json` (**Daten dürfen nicht zwischengespeichert werden**)

* `/libs/cq/i18n/*` (Internalisierung)

<!-- 

Comment Type: remark
Last Modified By: unknown unknown (ims-author-00AF43764F54BE740A490D44@AdobeID)
Last Modified Date: 2015-06-26T04:38:17.016-0400

<p>We need to highlight whether a path applies to all versions or specific ones.<br /> </p>

 -->

## Konfigurieren des Dispatchers zum Verhindern von CSRF-Angriffen {#configure-dispatcher-to-prevent-csrf-attacks}

AEM bietet ein [Framework](https://experienceleague.adobe.com/en/docs/experience-manager-release-information/aem-release-updates/previous-updates/aem-previous-versions#verification-steps), mit dem CSRF-Angriffe (Cross-Site Request Forgery) verhindert werden können. Um dieses Framework ordnungsgemäß zu verwenden, müssen Sie die CSRF-Token-Unterstützung im Dispatcher auf die Zulassungsliste gesetzt haben.
<!-- OLD URL ABOVE USED TO BE https://helpx.adobe.com/experience-manager/6-3/sites/administering/using/security-checklist.html#verification-steps -->
Gehen Sie dazu wie folgt vor:

1. Erstellen Sie einen Filter, um den Pfad `/libs/granite/csrf/token.json` zuzulassen.
1. Fügen Sie die Kopfzeile `CSRF-Token` dem Abschnitt `clientheaders` der Dispatcher-Konfiguration hinzu.

## Verhindern von Clickjacking {#prevent-clickjacking}

Um Clickjacking zu verhindern, sollten Sie Ihren Webserver so konfigurieren, dass er den HTTP-Header `X-FRAME-OPTIONS` bereitstellt, der auf `SAMEORIGIN` festgelegt ist.

Weitere Informationen zum Thema Clickjacking finden Sie auf der [OWASP-Website](https://owasp.org/www-community/attacks/Clickjacking).

## Durchführen eines Penetrationstests {#perform-a-penetration-test}

Adobe empfiehlt die Durchführung eines Penetrationstests für Ihre AEM-Infrastruktur, bevor Sie mit der Produktion beginnen.
