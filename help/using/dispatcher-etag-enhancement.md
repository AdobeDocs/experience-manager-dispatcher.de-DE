---
title: Dispatcher-E-Tag-Verbesserung für CDN-Verlängerung
description: Verfügbarkeit, Support-Status und Verhalten von INTERNAL_AEM_DISPATCHER_ETAG_ENHANCEMENT in AEM as a Cloud Service.
source-git-commit: ac0fafd060643903735ff565072ef2c5bee970be
workflow-type: tm+mt
source-wordcount: '308'
ht-degree: 0%

---

# Dispatcher-E-Tag-Verbesserung für CDN-Verlängerung

## Überblick

Mit dem `INTERNAL_AEM_DISPATCHER_ETAG_ENHANCEMENT`-Flag kann Dispatcher den `If-None-Match`-Header bei Cache-Treffern auswerten. Wenn der eingehende `If-None-Match` mit dem zwischengespeicherten `ETag` übereinstimmt, kann Dispatcher `304 Not Modified` anstelle von `200 OK` zurückgeben.

Dieses Verhalten wurde entwickelt, um unnötige Payload-Übertragungen zwischen CDN und Dispatcher zu reduzieren und die Effizienz des bedingten Caches zu erhöhen.

## Verfügbarkeit

- Dispatcher-Version: `2.0.264`
- AEM SDK Build: `aem-sdk-2026.2.24464.20260214T050318Z-260100`

## AEM as a Cloud Service-Support

In AEM as a Cloud Service wird diese Funktion für die Verwendung durch Kunden unterstützt.

Kunden können sie aktivieren, indem sie die Umgebungsvariable `INTERNAL_AEM_DISPATCHER_ETAG_ENHANCEMENT` in Cloud Manager festlegen. Adobe kann sie bei Bedarf auch im Namen des Kunden aktivieren.

Wenn diese Option aktiviert ist und das CDN `If-None-Match` sendet und das entsprechende `ETag` im Dispatcher-Cache vorhanden ist, werden höhere `304` zwischen CDN und Dispatcher erwartet. Diese Erhöhung ist das beabsichtigte Ergebnis.

## Konfigurationsbeispiel (Cache-ETag-Header)

Damit diese Verbesserung wirksam ist, stellen Sie sicher, dass Dispatcher den `ETag`-Antwort-Header zwischenspeichert und Ihr Webserver so konfiguriert ist, dass keine dateisystembasierten ETags generiert werden.

Beispiel `dispatcher.any` Cache-Abschnitt:

```text
/cache {
  /headers {
    "Cache-Control"
    "Content-Type"
    "Expires"
    "Last-Modified"
    "ETag"
  }
}
```

Beispielhafte Apache-Direktive im Dispatcher vhost-Kontext:

```apache
FileETag none
```

Eine allgemeine Anleitung zum Caching von Kopfzeilen finden Sie unter [Caching von HTTP-Antwort-Kopfzeilen](dispatcher-configuration.md#caching-http-response-headers).

## Validierungsbeispiel

Nach der Aktivierung der Umgebungsvariablen und der Bereitstellung der Konfigurationsänderungen:

1. Fordern Sie einmal an, den Cache zu erwärmen und die zurückgegebenen `ETag` zu erfassen.
1. Mit `If-None-Match: <etag-value>` erneut anfordern.
1. Bestätigen, dass Dispatcher `304 Not Modified` für Cache-Treffer-Wiederholungsflüsse zurückgibt.

## Öffentliche Referenz (verwandtes Verhalten)

Eine kundenorientierte Grundlinie zum Kopfzeilen-Caching und zur `ETag` in Dispatcher finden Sie unter:

- [Konfigurieren von Dispatcher - Zwischenspeichern von HTTP-Antwortkopfzeilen](https://experienceleague.adobe.com/de/docs/experience-manager-dispatcher/using/configuring/dispatcher-configuration#caching-http-response-headers)

„Diese Funktion ist in Dispatcher `2.0.264` (AEM SDK `2026.2.24464`) verfügbar. Wenn diese Option aktiviert ist, kann Dispatcher `If-None-Match` anhand zwischengespeicherter `ETag` validieren und `304 Not Modified` bei Cache-Treffern zurückgeben. In AEM as a Cloud Service wird dies unterstützt und kann über die Cloud Manager-Umgebungskonfiguration aktiviert werden.“
