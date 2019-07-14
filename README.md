# Gabarit Radio-Canada
Gabarit avec entete et pied de page Radio-Canada

- [Pre-requis](#pre-requis)
- [Form State](#form-state)
- [Google Analytics](#google-analytics)
- [Meta](#meta)
- [Omniture](#omniture)
- [Pixel](#pixel)
- [Clicktrackers](#clicktrackers)
- [Code :: Stylesheet](#code-stylesheet)
- [Code :: Javascript](#code-javascript)



## Pre-requis
Radio-Canada utilise plusieurs librairies par defaut. Il est important d'en tenir compte dans le developpement d'un site avec ce gabarit.
Les lignes suivantes sont obligatoire pour le bon fonctionnement des entetes et pieds de page

### Code obligatoire dans le HEAD
Le gabarit a une structure bien precise, vous devez coder a l'interieur du div WRAPPER.

Une partie de javascript est declare dans le HEAD pour s'assurer que les librairies obligatoires soient bien incluses dans le projet.

Le gabarit utilise la **version v1.10.2 de JQuery**. Le code est appele en "no conflict" ce qui signifie que si vous voulez utiliser JQuery directement, le symbole de "$" ne fonctionnera pas, vous devrez utiliser `jQuery('#selecteur')` a la place.


```
<link href="https://ici.radio-canada.ca/content/generated/main.min.css?v=1.0.0.26060" rel="stylesheet" type="text/css" />
<link rel="stylesheet" type="text/css" media="screen" href="https://ici.radio-canada.ca/Content/component.DynamicMenu.css" />
<link rel="stylesheet" type="text/css" media="screen" href="https://ici.radio-canada.ca/Content/component.StickyNav.css" />
<link rel="stylesheet" type="text/css" media="screen" href="https://ici.radio-canada.ca/Content/component.Menuzone.css" />
<link rel="stylesheet" type="text/css" media="screen" href="https://ici.radio-canada.ca/Content/component.Footer.css" />
<link rel="stylesheet" type="text/css" media="screen" href="https://s.radio-canada.ca/_css/modules/modules.barrepartagee.1.0.0.css" />
<link rel="stylesheet" type="text/css" media="screen" href="https://s.radio-canada.ca/mp/modules.socialbar.1.0.0.css" />

<script>
window.appHost = 'https://ici.radio-canada.ca';
</script>

<script src='https://ici.radio-canada.ca/Content/generated/main.min.js'></script>
<script src='https://ici.radio-canada.ca/Content/generated/app.min.js'></script>

<script src='https://geoip.radio-canada.ca/geoip.js'></script>
<script src='https://s.radio-canada.ca/_js/modules/modules.pub.manuel.js'></script>
<script src='https://s.radio-canada.ca/_js/modules/pub.fixnav.js'></script>
<script type="text/javascript" src="https://s.radio-canada.ca/mp/viafoura.js"></script>
<script type="text/javascript" src="https://s.radio-canada.ca/mp/modules.socialbar.executer.6.0.js"></script>
```

### Code obligatoire dans le BODY
Radio-Canada utilise sont propre "*requirejs*" pour traiter les javascript. vous pourrez voir du code "bunkerRequire" qui sert exactement de la meme facon que requirejs peut le faire.

Les lignes ci-dessous affichent les entete et pieds de page par appel asynchrone.
Meme chose pour les publicites.

```
<script src='https://s.radio-canada.ca/omniture/omni_stats_base.js?version=2014110501'></script>
```

```
<script>
var section = 'tele';
function loadAdvertising() {
	bunkerRequire(['underscore', 'services/componentsService', 'externals/components/adsmanager/main'], function(_, cpSvc, adsmanager) {
		adsmanager.init();
		adsmanager.processInit();
	});
}

bunkerRequire(['jquery', 'services/componentsService', 'underscore'], function(jq, cpSvc, _) {
	var navAsync = cpSvc.load('dynamicmenu', { hideleaderboard: true }, document.getElementById('hostNavigation'));
	var footerAsync = cpSvc.load('footer', {}, document.getElementById('hostFooter'));
	var stickyNavAsync = cpSvc.load('StickyNav', { itemSelector: '#hostNavigation', url: section }, document.getElementById('hostNavSticky'));
	var bannerAsync = cpSvc.load('banner', {}, document.getElementById('hostPubBanner'));
	if (section === 'tele') {
		var navZoneTeleASync = cpSvc.load('menuZone', {}, document.getElementById('hostNavigationZone'));
	}
	var bigBoxAsync = cpSvc.load('bigbox', { external: true }, document.getElementById('hostPubBigBox'));
	jq.when(navAsync, footerAsync, stickyNavAsync, bannerAsync, bigBoxAsync).done(function(nav, footer, sticky, banner, bigbox) {
		_.delay(loadAdvertising, 500);
	});
});
</script>
<script type="text/javascript">_satellite.pageBottom();</script>
```

Le code `<script type="text/javascript">_satellite.pageBottom();</script>` initie le suivi Omniture.


## Form State
Depuis 2019, nous ajoutons l'attribut "data-form-state", aux pages de type concours.
Cette attribut est ensuite recupere dans le css pour changer l'apparence de la page selon la valeur de l'attribut.

En general, c'est surtout le bouton de participation qui change de l'etat "Participer" a "Concours termine".

```
<html class="nojs" lang="fr" dir="ltr" data-form-state="!closed">
```

Il a ete entendu que de facon general, lorsque le concours est dans la periode active, la valeur est "*!closed*".

Ensuite, lorsque le concours est termine, on change la valeur pour "*closed*".

Cela permet a Stefan de faire le changement sans que nous ayons a intervenir, si jamais il voudrait effectuer le travail seul.

Dans le CSS, le changement d'etat se ferait comme suit:

```
html[data-form-state="!closed"] .bouton-de-participation{ background-image: url(../images/bouton-participer.png); }
html[data-form-state="closed"] .bouton-de-participation{ background-image: url(../images/bouton-terminer.png); }
```

## Google Analytics
Toujours mettre un code Google Analytics. Ce n'est pas la mesure officielle, mais le departement des vente s'y refere souvent.
Le code doit absolument avoir les attributs d'anonymisation de l'adresse IP active. Ce n'est pas une valeur par defaut du code Analytics.

```
<script async src="https://www.googletagmanager.com/gtag/js?id=UA-87186208-51&api=1"></script>
<script>
window.dataLayer = window.dataLayer || [];
function gtag(){dataLayer.push(arguments);}
gtag('js', new Date());
gtag('config', 'UA-87186208-51', { 'anonymize_ip': true });
</script>
```
Le code se trouve dans le URL `&api=1` et dans le code javascript `{'anonymize_ip' : true}`

## Meta
Les meta sont fournis par le charge de projet. Le code est souvent a verifier car il n'est pas toujours bien ecrit.
Voici la facon comment ce devrait etre ecrit.

```
<title> | ICI Radio-Canada.ca</title>
<meta name="Description" content="" />
<meta name="Keywords" content="" />
<meta name="dc.title" content=" | ICI Radio-Canada.ca" />
<meta name="dc.keywords" content="" />
<meta name="dc.description" content="" />
<meta property="og:url" content="" />
<meta property="og:type" content="website" />
<meta property="og:title" content=" | ICI Radio-Canada.ca" />
<meta property="og:description" content="" />
<meta property="og:image" content="" />
```


## Omniture
Le gabarit utilise les meta donnees pour faire le suivi Omniture.

### Initier Omniture
Le systeme de suivit est d'abord initie par un code javascript general.
Ce code doit se situer dans le HEAD.

```
<script src="https://assets.adobedtm.com/2eda04f28b1fa2bbcd3ec449dfdc174232ed3359/satelliteLib-fafff6f6fa41c8ef8818e2ad8c1bfb4776de2f18.js"></script>
```

Ce code doit se situer dans au debut du BODY.
```
<script src='https://s.radio-canada.ca/omniture/omni_stats_base.js?version=2014110501'></script>
```

### Valeur du projet
Les valeurs seront fournies sous forme de META. Les valeurs debutent tous par le nom "rc."
```
<meta name="rc.domaine" content="" />
<meta name="rc.application" content="" />
<meta name="rc.formatApplication" content="" />
<meta name="rc.section" content="" />
<meta name="rc.groupeSection" content="" />
<meta name="rc.segment" content="" />
<meta name="rc.pageSegment" content="" />
<meta name="rc.codePage" content="" />
<meta name="rc.niveau" content="" />
<meta name="rc.titre" content="" />
<meta name="rc.typeDocument" content="" />
<meta name="rc.typeContenu" content="" />
<meta name="rc.theme" content="" />
<meta name="rc.sousTheme" content="" />
<meta name="rc.dateCreation" content="" />
<meta name="rc.region" content="" />
<meta name="rc.blogue" content="" />
<meta name="rc.sport" content="" />
<meta name="rc.emission" content="" />
<meta name="rc.reseau" content="" />
<meta name="rc.chaine" content="" />
<meta name="rc.saison" content="" />
```

Egalement, Radio-Canada fait un suivi avec Webtrend, ne pas oublier d'ajouter les lignes.
```
<meta id="metaWebTrends" name="WT.sp" content="global;@@@5" />
<meta name="WT.clic" content="" />
```

## Pixel
Il arrive que le client souhait ajouter un pixel de "retargeting". Un espace est deja prevu a cet effet (ligne #78).
Le pixel est un code image. Vous pouvez en ajouter autant que vous le voulez, tant que les codes soient dans le div pixel.

```
<div id="pixel">
	<img src="http://lien-vers-le-pixel.jpg" alt="" height="1" width="1" />
</div>
```

## Clicktrackers
Le client pourrait fournir des code "clicktrackers". Ces code remplacent les URL cliquables.

SVP, s'assurer que les codes fournis fonctionnent correctement et que ce sont bien des redirection URL.
Parfois, ce sont des url de pixel qui vous seront fourni a la place.


## Code :: Stylesheet
### Google Font
Toujours utiliser des police Google. De cette facon, on s'assure que le site fonctionnera correctement sur tous les navigateurs sans avoir a les tester.
A l'exception, des polices Radio-Canada et FontAwesome.

### radio-canada.css
La feuille `radio-canada.css` se compose des elements suivants:
- Appels aux polices
- Cacher du contenu Radio-Canada


### style.css
Les breakpoints poour le responsive est inclus dans le code. Ce sont les formats les plus standard.
Si vous les respectez, vous aurez moins de probleme avec la compatibilite des navigateurs.

En pied de code, il y a aussi un espace nomme "demo". Ce code est en place pour aider au developpeur a placer les elements selon la maquette fournie.

## Code :: Javascript
Si vous deviez utiliser du javascript dans votre projet, mettre les js dans le pied du site.

**Votre code doit etre compatible avec JQuery v1.10.2**

Pour eviter tout conflit avec d'autres codes, il est preferable d'isoler vos declarations de cette facon:

```
(function($) {
	/// votre code
})(jQuery);
```
