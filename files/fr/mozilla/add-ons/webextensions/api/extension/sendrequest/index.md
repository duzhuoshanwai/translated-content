---
title: extension.sendRequest()
slug: Mozilla/Add-ons/WebExtensions/API/extension/sendRequest
---

{{AddonSidebar}}{{Deprecated_Header}}

> [!WARNING]
> Cette méthode est dépréciée. utilisez {{WebExtAPIRef("runtime.sendMessage")}} à la place.

Envoie une seule requête aux autres écouteurs de l'extension. Similaire à {{WebExtAPIRef('runtime.connect')}},mais envoie seulement une seule requête avec une réponse optionnelle. L'événement {{WebExtAPIRef('extension.onRequest')}} est déclenché dans chaque page de l'extension

## Syntaxe

```js
chrome.extension.sendRequest(
  extensionId,             // optional string
  request,                 // any
  function(response) {...} // optional function
)
```

Cette API est également disponible en tant que `browser.extension.sendRequest()` dans une [version qui renvoie une promise](/fr/docs/Mozilla/Add-ons/WebExtensions/API#callbacks_and_promises).

### Paramètres

- `extensionId`{{Optional_Inline}}
  - : `string`. L'ID d'extension de l'extension à laquelle vous souhaitez vous connecter. Si omis, la valeur par défaut est votre propre extension.
- `request`
  - : `any`.
- `responseCallback`{{Optional_Inline}}
  - : `function`. La fonction est passée les arguments suivants :
    - `response`
      - : `any`. Objet de réponse JSON envoyé par le gestionnaire de la requête. Si une erreur survient lors de la connexion à l'extension, le rappel sera appelé sans arguments et {{WebExtAPIRef('runtime.lastError')}} sera défini sur le message d'erreur.

## Compatibilité des navigateurs

{{Compat}}

{{WebExtExamples}}

> [!NOTE]
>
> Cette API est basée sur l'API Chromium [`chrome.extension`](https://developer.chrome.com/docs/extensions/reference/api/extension). Cette documentation est dérivée de [`extension.json`](https://chromium.googlesource.com/chromium/src/+/master/chrome/common/extensions/api/extension.json) dans le code Chromium.
>
> Les données de compatibilité relatives à Microsoft Edge sont fournies par Microsoft Corporation et incluses ici sous la licence Creative Commons Attribution 3.0 pour les États-Unis.

<!--
// Copyright 2015 The Chromium Authors. All rights reserved.
//
// Redistribution and use in source and binary forms, with or without
// modification, are permitted provided that the following conditions are
// met:
//
//    * Redistributions of source code must retain the above copyright
// notice, this list of conditions and the following disclaimer.
//    * Redistributions in binary form must reproduce the above
// copyright notice, this list of conditions and the following disclaimer
// in the documentation and/or other materials provided with the
// distribution.
//    * Neither the name of Google Inc. nor the names of its
// contributors may be used to endorse or promote products derived from
// this software without specific prior written permission.
//
// THIS SOFTWARE IS PROVIDED BY THE COPYRIGHT HOLDERS AND CONTRIBUTORS
// "AS IS" AND ANY EXPRESS OR IMPLIED WARRANTIES, INCLUDING, BUT NOT
// LIMITED TO, THE IMPLIED WARRANTIES OF MERCHANTABILITY AND FITNESS FOR
// A PARTICULAR PURPOSE ARE DISCLAIMED. IN NO EVENT SHALL THE COPYRIGHT
// OWNER OR CONTRIBUTORS BE LIABLE FOR ANY DIRECT, INDIRECT, INCIDENTAL,
// SPECIAL, EXEMPLARY, OR CONSEQUENTIAL DAMAGES (INCLUDING, BUT NOT
// LIMITED TO, PROCUREMENT OF SUBSTITUTE GOODS OR SERVICES; LOSS OF USE,
// DATA, OR PROFITS; OR BUSINESS INTERRUPTION) HOWEVER CAUSED AND ON ANY
// THEORY OF LIABILITY, WHETHER IN CONTRACT, STRICT LIABILITY, OR TORT
// (INCLUDING NEGLIGENCE OR OTHERWISE) ARISING IN ANY WAY OUT OF THE USE
// OF THIS SOFTWARE, EVEN IF ADVISED OF THE POSSIBILITY OF SUCH DAMAGE.
-->
