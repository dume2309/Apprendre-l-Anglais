# 🌍 APPRENDRE L'ANGLAIS — Cahier des charges complet & final
> **Dans Claude Code : ouvrir ce fichier et dire "Génère l'appli complète selon ce cahier des charges"**
> Repo    : https://github.com/dume2309/Apprendre-l-Anglais
> Fichier : `app/index.html` — tout CSS + HTML + JS dans un seul fichier

---

## 🎯 VISION

**Un voyage simulé de A à Z. Gusti observe tout. L'appli s'adapte en permanence.**

L'utilisateur ouvre l'appli, appuie sur "Commencer", et se retrouve dans l'avion.
Gusti joue tous les personnages, enchaîne les scènes, surveille les progrès en silence.
De temps en temps il sort un rapport. Pas à la demande. Lui qui décide.
Aucun menu. Aucun scénario à choisir. Gusti pilote tout.

---

## 🗣️ QUESTIONS EN FRANÇAIS — RÈGLE FONDAMENTALE

**Quand Dumè bloque sur quelque chose, il peut poser une question en français à tout moment.**
Gusti sort du personnage, répond, puis reprend la scène exactement où elle en était.
C'est fluide. Naturel. Comme avec un vrai coach.

### Détection des questions françaises
```javascript
// L'IA détecte si le message est une question en français (pas une réponse au scénario)
// Indices : point d'interrogation, "comment dit-on", "qu'est-ce que", "c'est quoi",
//           "je comprends pas", "tu peux expliquer", "ça veut dire quoi"
// Gusti répond alors HORS SCÈNE puis reprend
```

### Comportement de Gusti selon le type de question

#### "Comment dit-on X en anglais ?"
```
Dumè   : "Comment dit-on 'je veux changer de chambre' ?"

Gusti  : [hors scène]
         "I'd like to change my room, please."
         Ou plus fort : "I'm afraid this room isn't suitable — could you move me to another one?"
         La deuxième version est plus polie et plus efficace.
         [reprend la scène]
         Ready? I'm the receptionist again. Go ahead.
```

#### "Je comprends pas ce que tu as dit"
```
Dumè   : "Je comprends pas ta dernière phrase"

Gusti  : [hors scène]
         J'ai dit : "I'm afraid we're fully booked for that excursion."
         = Malheureusement nous sommes complets pour cette excursion.
         'I'm afraid' ici ne veut pas dire 'j'ai peur' — c'est une façon polie
         de donner une mauvaise nouvelle. Très courant en anglais.
         [reprend]
         On continue. Tu voulais savoir si il restait des places.
```

#### "C'est quoi la différence entre X et Y ?"
```
Dumè   : "C'est quoi la différence entre 'can' et 'could' ?"

Gusti  : [hors scène]
         'Can' = direct. "Can I have a towel?" → correct, un peu brusque.
         'Could' = poli. "Could I have a towel?" → toujours mieux dans un hôtel.
         En voyage : utilise presque toujours 'could'. Ça ouvre des portes.
         [reprend]
         Alright. Le réceptionniste attend ta demande.
```

#### "Comment on prononce X ?"
```
Dumè   : "Comment on prononce 'receipt' ?"

Gusti  : [hors scène]
         "ri-SEET" — le 'p' est muet. Comme 'receive'.
         Autres pièges courants : 'island' (le 's' est muet), 'debt' (le 'b' est muet).
         [reprend]
         L'addition arrive. Tu veux vérifier le montant.
```

#### "Je suis perdu, c'est quoi la bonne façon de dire ça ?"
```
Dumè   : "Je sais pas quoi répondre là"

Gusti  : [hors scène — donne 2-3 options selon le niveau]
         Option simple : "It's broken." (ça marchera)
         Option mieux  : "The air conditioning doesn't seem to be working."
         Option pro    : "I'm afraid the air conditioning unit in my room appears to be faulty."
         En phase 1-2 : utilise la version simple. Elle fonctionne.
         [reprend]
         Vas-y. Tu m'appelles depuis ta chambre.
```

#### "C'est quoi le niveau de politesse normal ?"
```
Dumè   : "C'est impoli de dire 'I want' ?"

Gusti  : [hors scène]
         Pas impoli. Mais perçu comme brusque par les anglophones.
         Hiérarchie de politesse :
         "I want a towel."          → brusque mais compris
         "I need a towel."          → neutre
         "Can I get a towel?"       → correct, décontracté
         "Could I have a towel?"    → poli, recommandé
         "I was wondering if I could possibly have a towel?" → trop formel pour un hôtel
         En voyage : reste sur 'could I have' et 'I'd like'. Toujours.
         [reprend]
         La réception décroche. Tu as besoin de serviettes.
```

### Règles de comportement de Gusti pour les questions FR

```
RÈGLE 1 — Toujours répondre en français quand la question est en français
           Exception : si la réponse est une expression anglaise à mémoriser,
           il donne l'expression en anglais + explication en français

RÈGLE 2 — Réponse courte et concrète
           Pas de cours magistral. 3-5 lignes maximum.
           Si c'est complexe → "On en parle à la fin de la session."

RÈGLE 3 — Toujours reprendre la scène après la réponse
           "On continue." ou "Ready?" ou "Alright, where were we..."
           La scène reprend exactement où elle en était.

RÈGLE 4 — Logger la question comme indicateur pédagogique
           [OBS:QUESTION_FR:floor_numbers] → Dumè a eu besoin d'aide sur ce point
           Compte dans les stats comme hésitation (pas comme erreur)

RÈGLE 5 — Si la même question revient en 3+ sessions
           Gusti l'intègre systématiquement dans les scènes futures
           Et en fait mention dans le prochain rapport :
           "Tu demandes souvent comment dire X. On va l'ancrer définitivement."

RÈGLE 6 — Jamais juger ou faire sentir la question stupide
           Même "comment dit-on bonjour" reçoit une vraie réponse.
           Gusti n'est pas surpris. Il a tout vu.
```

### Tag pour les questions FR dans les stats
```javascript
// Parsé par l'appli comme les autres tags
[OBS:QUESTION_FR:sujet]     → hésitation, besoin d'aide
[OBS:EXPLICATION:sujet]     → Gusti a expliqué quelque chose
```

### Affichage dans l'UI

Les messages "hors scène" de Gusti ont un style visuel distinct :
```css
.msg.hors-scene {
  background: var(--s3);
  border: 1px solid rgba(232,184,75,.25);
  border-left: 3px solid var(--gold);  /* ligne dorée à gauche */
  font-style: normal;
}
.msg.hors-scene .mtag {
  color: var(--gold);
  content: "🌺 GUSTI — hors scène";
}
/* Reprise de scène */
.msg.reprise {
  background: transparent;
  border: 1px dashed var(--bd);
  font-size: 12px;
  color: var(--muted);
  text-align: center;
  align-self: center;
  max-width: 70%;
}
```

---

## 📅 PLAN 6 MOIS — Objectifs réalistes

L'appli connaît ce plan. Gusti adapte contenu et niveau selon la phase.

### Phase 1 — Mois 1 : Fondations
**Contexte** : convalescence, temps disponible, motivation haute
**Rythme** : 3 sessions/semaine, 20-30 min
**Objectif** : couvrir tous les scénarios, identifier toutes les lacunes

```
Semaines 1-2 : Hôtel complet + Taxi + Aéroport
Semaines 3-4 : Restaurant + Shopping + Chiffres & Heures intensif
Score cible  : 45-55% moyenne (normal pour débuter)
```
Gusti en phase 1 : patient, pédagogue, beaucoup d'explications, traductions systématiques.
Répond à toutes les questions FR sans sourciller.

### Phase 2 — Mois 2-3 : Consolidation
**Rythme** : 3 sessions/semaine, 20-30 min
**Objectif** : éliminer lacunes critiques, 65%+ sur les scénarios de base

```
Mois 2 : Rejouer les scénarios faibles (< 50%) en priorité absolue
          Intensifier chiffres & heures
          Introduire Urgences + Excursions
Mois 3 : Enchaîner 2-3 scènes sans pause
          Vocabulaire plus complexe : négocier, se plaindre fermement
Score cible : 60-70% moyenne
```
Gusti en phase 2 : plus direct, corrections immédiates, commence à réduire les traductions.

### Phase 3 — Mois 4-5 : Fluidité
**Contexte** : tendon rétabli, pratique possible en voiture
**Rythme** : 3 sessions/semaine (certaines en voiture)
**Objectif** : fluidité, réactions spontanées, situations imprévues

```
Mois 4 : Sessions voyage complet (avion → hôtel → excursions → restaurant)
          Situations imprévues : grève, arnaque, malentendu
Mois 5 : Pression temporelle dans les scènes
          Personnages difficiles (impatients, accents forts)
Score cible : 75-85% moyenne
```
Gusti en phase 3 : exigeant, peu indulgent, situations stressantes. Traductions rares.

### Phase 4 — Mois 6 : Autonomie
**Objectif** : 85%+ partout, aucun blocage, prêt pour un vrai voyage

```
Sessions voyage complet sans aide (pas de traductions FR sauf question explicite)
Situations extrêmes : tout se passe mal dans le même voyage
Pratique libre : conversations sans scénario défini
Score cible : 85%+ | Aucun scénario sous 70%
```
Gusti en phase 4 : presque silencieux, intervient peu. "Good." suffit.

### Après 6 mois — Maintien
```
Rythme : 1-2 sessions/semaine
Objectif : ne jamais régresser
Sessions thématiques selon voyages prévus
Gusti reste là — il connaît Dumè depuis le début
Rapport mensuel de maintien
```

---

## 🗺️ LE VOYAGE SIMULÉ — Toutes les scènes

```
✈️  AVION
    → conversation voisin (origin, destination, why traveling)
    → hôtesse : boisson, couverture, médicament (tendon — contexte Dumè)
    → annonces pilote (comprendre)
    → chiffres : altitude, durée vol, heure arrivée

🛬  AÉROPORT ARRIVÉE
    → immigration : purpose of visit, duration, where staying
    → bagages perdus → réclamation
    → bagages abîmés → compensation
    → currency exchange : chiffres, taux, combien

🚕  TAXI
    → négocier prix fixe vs compteur
    → donner adresse précise
    → chauffeur prend mauvais chemin → réagir
    → chiffres : prix, monnaie rendue, pourboire

🏨  HÔTEL CHECK-IN
    → donner nom, confirmer réservation
    → type chambre (double/twin, vue, calme, étage)
    → chambre pas prête → bagagerie, revenir quand
    → WiFi, petit-dej (heure, inclus), piscine
    → PIÈGE : ground floor ≠ first floor (UK vs US)
    → chiffres : numéro chambre, étage, heure

🏨  HÔTEL PROBLÈMES
    → clim cassée → appeler, expliquer, insister
    → manque serviettes / papier WC / oreiller
    → lit trop dur → demander matelas en plus
    → voisins bruyants → escalader si besoin
    → room service → commander, heure, prix
    → chiffres : heure réparation, délai, numéro chambre

🏨  HÔTEL CHECKOUT
    → demander la note
    → erreur facture → contester
    → late checkout → négocier
    → laisser bagages → récupérer quand
    → chiffres : montant, erreur, heure

🏖️  EXCURSIONS
    → demander ce qui est dispo
    → réserver : bateau, snorkeling, temples, rizières
    → prix 2 personnes, groupe, enfants
    → horaires, RDV, durée, quoi apporter
    → annulation pluie → remboursement ou report
    → chiffres : prix/personne, heure départ, durée

🛍️  SHOPPING
    → demander prix
    → négocier : contre-proposition
    → tailles (S/M/L + chiffres), couleurs
    → article défectueux → retour
    → chiffres : prix, négociation, tailles numériques

🍽️  RESTAURANT RÉSERVATION
    → réserver : combien, heure, nom
    → extérieur/intérieur, occasion spéciale
    → complet → alternative

🍽️  RESTAURANT COMMANDER
    → menu, plats du jour, recommandations
    → allergies (noix, gluten, crustacés)
    → cuisson (rare/medium/well done)
    → chiffres : quantités, prix

🍽️  RESTAURANT PROBLÈMES
    → plat froid, mauvaise commande
    → attente trop longue
    → escalader au manager

🍽️  RESTAURANT ADDITION
    → demander l'addition
    → erreur sur facture → contester
    → payer séparément
    → tip : inclus ou non, combien
    → chiffres : lire addition, calculer tip

🚨  PHARMACIE / MÉDECIN
    → décrire symptômes
    → tendon, douleur pied (contexte Dumè)
    → allergie médicamenteuse
    → chiffres : dosage, fréquence, prix

🚨  PERTE & VOL
    → portefeuille volé → police, déclaration
    → passeport perdu → ambassade
    → téléphone perdu → bloquer SIM
    → chiffres : dates, numéros de référence

🔢  CHIFFRES & HEURES (transversal — toutes les scènes)
    → PIÈGES : fifteen/fifty, thirteen/thirty
    → Heures : o'clock, half past, quarter to/past
    → Étages : ground floor (UK) = first floor (US)
    → Dates : the 15th of April / April 15th
    → Téléphones : digit by digit
    → Prix : cent, thousand, "thirty-four ninety-nine"
```

---

## 🧠 AUTOADAPTATION — Résumé technique

```javascript
// Au démarrage de chaque session
const systemPrompt = buildGustiPrompt(stats);
// → Gusti reçoit les stats, les lacunes, la phase, les lacunes persistantes

// Pendant la session
parseReply(raw) // → extrait [OBS:LACUNE:x], [OBS:ACQUIS:x], [OBS:QUESTION_FR:x]
// → accumulés en mémoire de session

// Gusti adapte en temps réel
// Si même lacune 2x → recrée une situation pour la pratiquer
// Si question FR → sort du personnage, répond, reprend
// Si niveau bon → accélère, complique

// En fin de session [BILAN]...[/BILAN]
// → score, scénarios pratiqués, lacunes, acquis, conseil
// → saveToLocalStorage()
// → syncToGitHub()  // silencieux

// Vérification rapport spontané
const reportType = shouldTriggerReport(stats);
if (reportType) generateReport(reportType);
// Types : initial (session 3), weekly, monthly, alert, breakthrough, phase_end, 6months
```

---

## 📱 PLATEFORME — iPad / iPhone Safari UNIQUEMENT

```css
/* Obligatoire */
* { -webkit-tap-highlight-color: transparent; }
html, body { height: 100%; overflow: hidden; }
.scr { height: 100dvh; }                    /* dvh pas vh */
.scroll { -webkit-overflow-scrolling: touch; }
input { font-size: 16px; }                  /* évite zoom iOS */
.inp-row { padding-bottom: env(safe-area-inset-bottom, 16px); }
button { min-height: 44px; }               /* zone tactile Apple */
```

---

## 🎨 DESIGN

```css
:root {
  --bg:    #0c0e14;  --s1: #13161f;  --s2: #191d2d;  --s3: #20253a;
  --bd:    #282d45;  --gold: #e8b84b; --teal: #3ecfb2; --red: #f06b6b;
  --blue:  #5b9cf5;  --green: #4ade80; --text: #e4e6f2; --muted: #5f6480;
  --r: 12px;  --fn: 'Sora', sans-serif;
}
```
Fonts : **Sora** (corps) + **Lora** (titres) via Google Fonts

---

## 🖥️ INTERFACE — 2 écrans seulement

### Home (#sh)
```
✈️  Apprendre l'Anglais

🌺 Gusti
« [citation rotative] »

[ Sessions ] [ Échanges ] [ Score ] [ Jours ]

⚠️ 2 lacunes critiques    ← informatif
📅 Dernière session : hier
✅ GitHub sync : 22:47

[ 🎯 Commencer ]          ← UN SEUL BOUTON
```

### Chat (#sc)
```
← 🌺 Gusti · Session    [📊] [⚙️]
─────────────────────────────
Échanges: 8  ★★☆
─────────────────────────────

  [msg Gusti — en scène]
              [msg Dumè]
  [msg Gusti — hors scène ← style doré]
  [── reprise de scène ──]
              [msg Dumè]
  [msg Gusti — en scène]

─────────────────────────────
[ Écris en anglais... ou pose une question en français ] [➤]
```

**Placeholder de l'input** : `"Réponds en anglais… ou pose une question en français"`

**Pas de suggestions pré-écrites** — Dumè écrit librement. Plus réaliste, plus formateur.

---

## 🔄 SYNC GITHUB

```javascript
async function syncToGitHub(data) {
  const token = localStorage.getItem('gh_token');
  if (!token) return;
  const repo = 'dume2309/Apprendre-l-Anglais';
  const path = 'suivi/progression.json';

  try {
    const getRes = await fetch(
      `https://api.github.com/repos/${repo}/contents/${path}`,
      { headers: { Authorization: `Bearer ${token}`, Accept: 'application/vnd.github.v3+json' } }
    );
    const existing = getRes.ok ? await getRes.json() : {};
    const sha = existing.sha || null;
    const content = btoa(unescape(encodeURIComponent(JSON.stringify(data, null, 2))));

    await fetch(`https://api.github.com/repos/${repo}/contents/${path}`, {
      method: 'PUT',
      headers: {
        Authorization: `Bearer ${token}`,
        Accept: 'application/vnd.github.v3+json',
        'Content-Type': 'application/json'
      },
      body: JSON.stringify({
        message: `📊 Auto-sync ${new Date().toLocaleDateString('fr-FR')}`,
        content,
        ...(sha && { sha })
      })
    });
    updateSyncIndicator('ok');
  } catch(e) {
    updateSyncIndicator('error');
  }
}
```

Config GitHub (première ouverture) : modal simple avec champ token + bouton Valider.
Token stocké dans localStorage. Jamais dans le code.

---

## 📊 RAPPORTS SPONTANÉS

Gusti décide quand les sortir. Ils apparaissent dans le chat.

| Déclencheur | Type | Contenu |
|---|---|---|
| Session 3 | initial | Premier bilan, lacunes identifiées |
| Toutes les semaines | weekly | Progression, points à travailler |
| Toutes les 10 sessions | milestone | Bilan intermédiaire |
| Lacune persistante 3+ sessions | alert | Alerte, plan d'attaque |
| Score scénario +25% | breakthrough | Célébration sobre |
| Fin phase (session 12/36/60) | phase_end | Bilan de phase, passage au niveau suivant |
| Session 72 (~6 mois) | 6months | Bilan complet, recommandations long terme |

---

## 💾 LOCALSTORAGE — Clé `anglais_v1`

```json
{
  "meta": {
    "total_sessions": 0, "total_echanges": 0,
    "jours_actifs": [], "derniere_session": null,
    "derniere_sync_github": null, "dernier_rapport": null,
    "sessions_au_dernier_rapport": 0
  },
  "lacunes_globales": [],
  "scenarios": {
    "hotel_arrivee": {
      "sessions": 0, "scores": [], "score_moyen": null,
      "lacunes_cumulees": [], "questions_fr": [], "acquis": [],
      "derniere_session": null
    }
    // ... tous les scénarios avec la même structure
  },
  "historique": []
}
```

Scénarios : `hotel_arrivee`, `hotel_demandes`, `hotel_problemes`, `hotel_checkout`,
`taxi_commander`, `taxi_negocier`, `taxi_directions`, `transport_bus`,
`resto_reserver`, `resto_commander`, `resto_problemes`, `resto_addition`,
`excursion_reserver`, `excursion_groupe`, `excursion_annul`,
`shopping_prix`, `shopping_tailles`, `urgence_sante`, `urgence_papiers`,
`nombres_heures`, `pratique_libre`

---

## 👂 EXERCICES DE COMPRÉHENSION — "Qu'est-ce que tu as compris ?"

**Gusti teste la compréhension passive en plus de la production active.**
C'est le vrai indicateur de progression : comprendre sans demander de répéter.
Ces exercices sont intégrés naturellement dans les scènes — jamais annoncés comme "exercice".

### Principe
Gusti joue un personnage qui dit quelque chose en anglais (annonce, explication, description).
Il s'arrête et demande à Dumè de reformuler en français ce qu'il a compris.
Puis il corrige, complète, et note le niveau de compréhension.

```
[OBS:COMPREHENSION:75]    → a compris 75% du message
[OBS:COMPREHENSION:40]    → a raté des éléments importants
[OBS:COMPREHENSION:100]   → compréhension parfaite
```

Ce score alimente les stats séparément du score de production.
Gusti adapte la complexité et la vitesse selon les résultats.

---

### Situations de compréhension intégrées dans chaque scène

#### ✈️ Dans l'avion — Annonce pilote
```
Gusti joue le pilote :
"Ladies and gentlemen, this is your captain speaking. We are currently
flying at an altitude of thirty-five thousand feet. We're expecting some
turbulence over the next twenty minutes due to a weather system ahead.
Please fasten your seatbelts and remain seated. We anticipate arriving
in Denpasar approximately fifteen minutes ahead of schedule.
Local time is currently two forty-five pm. The temperature on the ground
is a warm thirty-one degrees. Sit back and enjoy the rest of the flight."

Gusti (hors scène) : "Qu'est-ce que le pilote vient de dire ?"

Dumè doit reformuler : altitude, turbulences, heure d'arrivée, météo, température

Gusti évalue et complète ce qui manque.
[OBS:COMPREHENSION:XX]
[OBS:LACUNE:altitude_numbers] si raté
[OBS:LACUNE:time_expressions] si heure pas comprise
```

#### ✈️ Dans l'avion — Annonce hôtesse
```
Gusti joue l'hôtesse :
"We'll be serving a choice of chicken with rice or a vegetarian pasta today.
Drinks are complimentary. We also have a selection of snacks available for
purchase. The duty-free trolley will be coming around shortly.
Please have your payment ready — we accept card only, no cash."

Gusti : "Qu'est-ce qu'elle a dit sur les repas et le paiement ?"
```

#### 🛬 Aéroport — Annonce retard ou changement de porte
```
Gusti joue l'annonce aéroport :
"Attention passengers on flight GA four oh seven to Lombok.
Due to a technical issue, your departure has been delayed by approximately
one hour and forty minutes. The new departure time is eighteen twenty.
Please proceed to gate D fourteen for boarding. We apologize for any
inconvenience caused."

Gusti : "C'était pour quel vol ? Qu'est-ce qui se passe ? Tu fais quoi maintenant ?"
```

#### 🏨 Hôtel — Message vocal ou annonce réception
```
Gusti joue le message de la réception (appel dans la chambre) :
"Good morning, Mr Dumè. This is a courtesy call from the front desk.
Just a reminder that check-out time is eleven am today. If you'd like
a late check-out, please let us know before ten and we'll do our best
to accommodate you, subject to availability. Breakfast is served until
ten thirty in the garden restaurant on the ground floor.
Have a wonderful day."

Gusti : "Qu'est-ce qu'ils te demandent ? T'as jusqu'à quand pour quoi ?"
```

#### 🏖️ Excursion — Briefing du guide
```
Gusti joue le guide d'excursion :
"Okay everyone, welcome to our snorkeling trip. A few things before we start.
Life jackets are mandatory — no exceptions. Please don't touch the coral,
it's very fragile and protected. We'll have two stops today: the first
lasts about forty-five minutes, the second about thirty. Lunch is included
and will be served on the boat between the two stops. If anyone feels
unwell or uncomfortable in the water, just signal to one of our guides —
there's no pressure. Any questions before we jump in?"

Gusti : "Qu'est-ce qui est obligatoire ? Combien de temps au total ? Qu'est-ce qui est inclus ?"
```

#### 🍽️ Restaurant — Serveur décrit le plat du jour
```
Gusti joue le serveur :
"Today's special is a pan-seared sea bass with a lemon butter sauce,
served with roasted seasonal vegetables and a saffron risotto.
The fish is locally caught this morning. For dessert, the chef recommends
the mango panna cotta — it's dairy-free and absolutely wonderful.
Can I get you something to drink while you look at the menu?"

Gusti : "C'est quoi le plat du jour ? Il y a quoi dedans ? Et le dessert ?"
```

#### 🚕 Taxi — Le chauffeur explique quelque chose
```
Gusti joue le chauffeur :
"Sorry boss, we have to take a different road today. There's a big ceremony
in the center — a temple festival. Happens every six months. The main road
is completely blocked. My way is a bit longer — maybe fifteen minutes more —
but it's the only way, no choice. Don't worry, the meter is off,
I give you fixed price, same as we said."

Gusti : "Pourquoi il prend un autre chemin ? Combien de temps en plus ? Et le prix ?"
```

#### 🚨 Urgence — Instructions du médecin ou pharmacien
```
Gusti joue le pharmacien :
"These are anti-inflammatory tablets. Take one tablet twice a day —
morning and evening — with food. Don't take them on an empty stomach
or you'll feel nauseous. Avoid alcohol while you're on these.
The course is five days. If you don't see improvement after three days
or if the swelling gets worse, you need to see a doctor.
Do you have any allergies to aspirin or ibuprofen?"

Gusti : "Combien de comprimés par jour ? Quand ? Avec quoi ? Combien de jours ?"
```

---

### Niveaux de difficulté — adaptés par Gusti selon la phase

```
Phase 1 : Annonces courtes, vocabulaire simple, débit lent
          Gusti répète si demandé, sans pénalité
          Questions larges : "Qu'est-ce qu'il a dit en gros ?"

Phase 2 : Annonces moyennes, vocabulaire varié, débit normal
          Une seule répétition autorisée
          Questions précises : "Quelle heure exactement ? Quel numéro de porte ?"

Phase 3 : Annonces longues, vocabulaire technique, débit rapide
          Pas de répétition — comme dans la vraie vie
          Questions multiples : heure + lieu + action + condition

Phase 4 : Annonces avec bruit de fond, accent fort, débit très rapide
          Zéro répétition
          "Tu as 30 secondes pour me dire tout ce que tu as compris."
```

### Scoring de la compréhension

```javascript
// Séparé du score de production
// Calculé par Gusti : éléments clés du message vs éléments restitués par Dumè
// Ex: message avec 5 infos clés → Dumè en restitue 3 → score compréhension 60%

stats.scenarios[id].score_comprehension  // score moyen compréhension
stats.scenarios[id].score_production     // score moyen production (parler)
stats.scenarios[id].score_moyen          // moyenne des deux

// Dans les rapports de Gusti :
// "Tu comprends bien mais tu parles encore avec hésitation." → compréhension > production
// "Tu parles bien mais tu rates des détails quand on te parle vite." → production > compréhension
```

### Dans les rapports spontanés de Gusti
```
📊 Compréhension vs Production :

  Compréhension : 71%  ████████░░
  Production    : 58%  ██████░░░░

"Tu comprends plutôt bien quand on parle lentement.
Moins bien quand ça s'accélère — les chiffres et les heures t'échappent encore.
On va travailler ça."
```

---

### Règles de Gusti pour les exercices de compréhension

```
RÈGLE 1 — Jamais annoncer "exercice de compréhension"
           Ça arrive naturellement dans la scène.
           Gusti joue le personnage, s'arrête, et demande.

RÈGLE 2 — Toujours demander en français
           "Qu'est-ce qu'il a dit ?" / "Tu as compris quoi ?"
           Dumè répond en français — c'est un test de compréhension, pas de production.

RÈGLE 3 — Corriger ce qui manque, féliciter ce qui est juste
           "Bien — t'as eu l'heure et le numéro de porte.
            T'as raté le motif du retard : problème technique."

RÈGLE 4 — Adapter la vitesse selon les résultats
           Si compréhension < 50% → ralentir dans les scènes suivantes
           Si compréhension > 80% → accélérer, accent plus marqué

RÈGLE 5 — Les chiffres et heures sont TOUJOURS dans les annonces
           C'est le point faible connu → intégrer systématiquement
           des nombres, heures, durées, prix dans chaque annonce

RÈGLE 6 — En phase 4, pas de répétition possible
           "In real life, the pilot says it once. So do I."
```

---

## 🎙️ DÉBIT DE PAROLE PROGRESSIF — Méthode d'acquisition naturelle

**Le cerveau a besoin de temps pour décoder avant de traiter à vitesse normale.**
Gusti adapte son débit selon la phase — lent et articulé au début, naturel et rapide à la fin.
C'est la méthode de tous les bons profs de langue. Ça s'applique à TOUTES les situations :
scènes jouées, annonces, descriptions, explications.

---

### Les 4 niveaux de débit

#### Phase 1 — Débit lent, articulation maximale
```
Instruction IA : "Speak slowly and clearly. Pause between each sentence.
                  Articulate every word distinctly. No contractions.
                  No reductions. No slang. Like a language teacher."

Exemple hôtel :
"Good... morning... Mister Dumè.
 Welcome... to the Grand Hotel.
 May I... have your name... please ?"

Exemple annonce pilote :
"Ladies and gentlemen...
 This is your captain... speaking.
 We are flying... at thirty-five thousand feet.
 We expect... some turbulence... in twenty minutes."
```

#### Phase 2 — Débit normal, prononciation soignée
```
Instruction IA : "Speak at normal pace but with clear pronunciation.
                  Use basic contractions (I'm, we're, you'll, it's).
                  No reductions. No slang. Like a professional."

Exemple hôtel :
"Good morning Mr Dumè. Welcome to the Grand Hotel.
 May I have your name please ?"

Exemple annonce pilote :
"Ladies and gentlemen, this is your captain speaking.
 We're flying at thirty-five thousand feet.
 We're expecting some turbulence in about twenty minutes."
```

#### Phase 3 — Débit naturel, contractions et réductions
```
Instruction IA : "Speak at natural conversational pace.
                  Use full range of contractions and natural reductions :
                  gonna, wanna, d'you, g'morning, t'the, kinda, sorta, lemme.
                  Drop unstressed syllables as native speakers do.
                  Vary your intonation naturally."

Exemple hôtel :
"G'morning Mr Dumè! Welcome t'the Grand Hotel.
 D'you have a reservation with us ?"

Exemple annonce pilote :
"Ladies'n'gentlemen, this's your captain speakin'.
 We're currently cruisin' at thirty-five thousand feet.
 We're gonna experience some turbulence in the next twenty minutes or so."

Réductions courantes à intégrer :
  "going to"  → "gonna"
  "want to"   → "wanna"
  "have to"   → "hafta"
  "do you"    → "d'you" / "dyou"
  "good morning" → "g'morning"
  "to the"    → "t'the"
  "kind of"   → "kinda"
  "let me"    → "lemme"
  "give me"   → "gimme"
  "out of"    → "outta"
  "a lot of"  → "alotta"
```

#### Phase 4 — Débit rapide + accents variés
```
Instruction IA : "Speak fast, naturally, with a specific accent.
                  No accommodation for the learner. Real world speed.
                  Use the accent specified below.
                  The learner must adapt — no repetition unless explicitly asked,
                  and asking costs a comprehension point."

Accents à faire varier selon les sessions :

🇬🇧 Britannique standard (RP)
   → "r" non prononcé en fin de mot, "a" long dans 'bath/path/can't'
   → "t" très articulé, intonation descendante
   → Exemples : réceptionniste londonien, agent d'immigration UK

🇦🇺 Australien
   → voyelles très différentes, "day" sonne comme "die"
   → intonation montante en fin de phrase
   → Exemples : guide d'excursion, co-passager dans l'avion

🇮🇳 Indien anglophone
   → "v" et "w" interchangeables, rythme syllabique
   → très courant dans les hôtels et services en Asie
   → Exemples : réceptionniste, chauffeur

🇿🇦 Sud-africain
   → voyelles particulières, accent distinctif
   → courant dans les lodges et safari

🏝️ Balinais / Asie du Sud-Est
   → accent que Gusti lui-même a naturellement
   → très courant pour Dumè vu ses voyages Bali/Lombok
   → Exemples : guide excursion, vendeur de marché, staff hôtel

🇺🇸 Américain (neutre puis régional)
   → Phase 3 : américain neutre (newscaster)
   → Phase 4 : américain du Sud ou New-Yorkais

Instruction spécifique Phase 4 :
"In real life, the pilot says it once with his accent.
 The taxi driver speaks fast and doesn't slow down for tourists.
 The market vendor has a strong local accent.
 So do I. Adapt."
```

---

### Application dans les exercices de compréhension

```
Phase 1 : Annonce pilote lente et articulée
          Gusti répète si demandé, sans pénalité

Phase 2 : Annonce à vitesse normale
          Une répétition autorisée, sans pénalité

Phase 3 : Annonce naturelle avec contractions
          Une répétition autorisée mais notée
          [OBS:REPETITION_DEMANDEE:annonce_pilote]

Phase 4 : Annonce rapide avec accent
          Zéro répétition
          "In real life, the pilot says it once. So do I."
          Si Dumè demande quand même → Gusti refuse gentiment :
          "You wouldn't ask the pilot to repeat. What did you catch ?"
```

---

### Gusti informe Dumè du changement de niveau

Entre les phases, Gusti l'annonce naturellement en français, dans le chat :
```
Phase 1 → 2 :
"Bien. Je vais parler un peu plus vite maintenant. Toujours clair,
mais à une vitesse normale. C'est comme ça qu'on parle vraiment."

Phase 2 → 3 :
"On passe à la vitesse naturelle. Tu vas entendre 'gonna', 'wanna',
'd'you'. C'est de l'anglais normal — pas de l'anglais de manuel.
Si tu bloques, demande-moi en français."

Phase 3 → 4 :
"Dernière étape. Je vais varier les accents d'une session à l'autre.
Australien, indien, britannique... Parce qu'en vrai voyage,
tu tombes sur tout. Pas de répétition. Adapte-toi."
```

---

### Suivi dans les stats

```javascript
stats.scenarios[id].score_comprehension_phase1  // baseline lent
stats.scenarios[id].score_comprehension_phase2  // vitesse normale
stats.scenarios[id].score_comprehension_phase3  // naturel avec réductions
stats.scenarios[id].score_comprehension_phase4  // rapide avec accent

// Dans les rapports Gusti :
"Compréhension à vitesse lente : 85% — bon.
 Compréhension à vitesse normale : 67% — à améliorer.
 Les chiffres t'échappent encore quand ça va vite.
 C'est le prochain objectif."
```

---

### Progression réaliste attendue

```
Mois 1  : comprend 60-70% à débit lent
Mois 2  : comprend 65-75% à débit normal
Mois 3  : comprend 60-70% à débit naturel (régression apparente — normale)
Mois 4  : comprend 70-80% à débit naturel
Mois 5  : comprend 65-75% avec accents variés
Mois 6  : comprend 80%+ avec n'importe quel accent à vitesse normale

Gusti dans le rapport 6 mois :
"Tu comprends maintenant un Australien qui parle vite.
 Quand tu es arrivé, tu ne comprenais pas un Balinais qui parlait lentement.
 C'est ça, six mois."
```

---

## 🎤 MODE VOCAL — Parler, pas écrire

**En vrai voyage on parle. On n'écrit pas.**
L'appli fonctionne principalement en vocal — micro pour parler, synthèse vocale pour écouter.
Le texte s'affiche quand même dans le chat pour référence et apprentissage visuel.
C'est aussi le mode parfait pour la voiture une fois le tendon rétabli.

---

### Web Speech API — Safari iOS natif

```javascript
// ── RECONNAISSANCE VOCALE (parole → texte) ──
const SpeechRecognition = window.SpeechRecognition || window.webkitSpeechRecognition;
const recognition = new SpeechRecognition();

recognition.continuous = false;       // s'arrête après une phrase
recognition.interimResults = true;    // affiche le texte en temps réel pendant la parole
recognition.maxAlternatives = 1;

// Langue selon le contexte
function startListening(mode = 'english') {
  recognition.lang = mode === 'french' ? 'fr-FR' : 'en-US';
  recognition.start();
}

recognition.onresult = (event) => {
  const transcript = event.results[0][0].transcript;
  const isFinal = event.results[0].isFinal;

  if (isFinal) {
    displayUserMessage(transcript);
    sendToGusti(transcript);
  } else {
    showInterimText(transcript); // affiche en grisé pendant la parole
  }
};

recognition.onerror = (event) => {
  if (event.error === 'no-speech') showHint("Je n'ai rien entendu — réessaie");
  if (event.error === 'not-allowed') showHint("Autorise le micro dans Safari");
};

// ── SYNTHÈSE VOCALE (texte → parole) ──
function gustiSpeak(text, phase) {
  // Nettoyer le texte — enlever les balises [FR:...] [VOCAB:...] etc.
  const cleanText = stripTags(text);

  const utterance = new SpeechSynthesisUtterance(cleanText);
  utterance.lang = 'en-US';

  // Débit selon la phase
  const rates = { 1: 0.75, 2: 0.95, 3: 1.1, 4: 1.25 };
  utterance.rate = rates[phase] || 1.0;

  // Voix : choisir la meilleure voix anglaise disponible sur iOS
  const voices = speechSynthesis.getVoices();
  const preferred = voices.find(v =>
    v.lang === 'en-US' && v.name.includes('Samantha')  // voix iOS naturelle
  ) || voices.find(v => v.lang === 'en-US');
  if (preferred) utterance.voice = preferred;

  utterance.pitch = 1.0;
  utterance.volume = 1.0;

  speechSynthesis.cancel(); // annuler si déjà en train de parler
  speechSynthesis.speak(utterance);
}

// Nettoyer les balises avant de lire à voix haute
function stripTags(text) {
  return text
    .replace(/\[FR:.*?\]/gs, '')
    .replace(/\[VOCAB:.*?\]/gs, '')
    .replace(/\[✓.*?\]/gs, '')
    .replace(/\[OBS:.*?\]/gs, '')
    .replace(/\[BILAN\][\s\S]*?\[\/BILAN\]/g, '')
    .trim();
}
```

---

### Interface vocale

#### Bouton micro principal
```
┌─────────────────────────────┐
│                              │
│   [msg Gusti — affiché]     │
│                              │
│         [msg Dumè]          │
│                              │
├─────────────────────────────┤
│                              │
│         🎤                  │  ← grand bouton central
│   Appuie et parle            │
│                              │
│  [⌨️ clavier]  [🔊 écouter] │  ← options secondaires
└─────────────────────────────┘
```

#### États du bouton micro
```css
/* Repos */
.mic-btn { background: var(--gold); border-radius: 50%; width: 72px; height: 72px; }

/* En écoute — pulse animé */
.mic-btn.listening {
  background: var(--red);
  animation: pulse 1s infinite;
  box-shadow: 0 0 0 12px rgba(240,107,107,.2);
}

/* Traitement — spinner */
.mic-btn.processing { background: var(--muted); animation: spin 1s linear infinite; }

@keyframes pulse {
  0%,100% { box-shadow: 0 0 0 0 rgba(240,107,107,.4); }
  50%      { box-shadow: 0 0 0 16px rgba(240,107,107,0); }
}
```

#### Affichage temps réel pendant la parole
```
┌─────────────────────────────┐
│ 🎤 En écoute...              │
│                              │
│ " I would like to check— "  │  ← texte interim en grisé, se construit en direct
└─────────────────────────────┘
```

---

### Détection automatique français / anglais

```javascript
// Si Dumè parle français → Gusti sort du personnage et répond en français
// Détection : si recognition.lang = 'en-US' mais résultat contient
// des mots français → re-processer comme question FR

const FRENCH_INDICATORS = [
  'comment', 'qu\'est-ce', 'c\'est quoi', 'pourquoi', 'je comprends pas',
  'tu peux', 'ça veut dire', 'comment dit-on', 'quelle est la différence',
  'je sais pas', 'j\'ai pas compris', 'répète', 'trop vite'
];

function detectLanguage(transcript) {
  const lower = transcript.toLowerCase();
  const isFrench = FRENCH_INDICATORS.some(w => lower.includes(w));
  return isFrench ? 'french' : 'english';
}

// Si français détecté → mode question FR → Gusti répond hors scène
// La réponse de Gusti est lue en français (lang: fr-FR) puis reprend en anglais
```

---

### Débit vocal progressif — correspondance phases

```javascript
const SPEECH_CONFIG = {
  1: {
    rate: 0.75,           // très lent, articulé
    pitch: 1.0,
    pauseBetweenSentences: 800,  // ms de silence entre les phrases
    accent: 'standard',
    description: 'Lent et articulé — chaque mot distinct'
  },
  2: {
    rate: 0.95,           // vitesse normale
    pitch: 1.0,
    pauseBetweenSentences: 400,
    accent: 'standard',
    description: 'Vitesse normale, prononciation soignée'
  },
  3: {
    rate: 1.1,            // légèrement rapide, naturel
    pitch: 1.0,
    pauseBetweenSentences: 200,
    accent: 'standard',
    description: 'Naturel — contractions, liaisons'
  },
  4: {
    rate: 1.25,           // rapide, accent varié
    pitch: 1.0,
    pauseBetweenSentences: 100,
    accent: 'varied',     // change selon la session
    description: 'Rapide — accents variés, monde réel'
  }
};
```

---

### Mode voiture 🚗

Activé manuellement via bouton ⚙️ ou automatiquement si session > 20 min sans interaction écran.
**Tout en audio. Zéro besoin de regarder l'écran.**

```javascript
const CAR_MODE = {
  // Gusti simplifie ses questions pour le mode mains-libres
  promptAddition: `
    CAR MODE ACTIVE:
    - Keep all your questions very short and simple
    - One question at a time only
    - Wait for a clear answer before continuing
    - No complex scenarios requiring visual reference
    - If the user says "répète" or "encore" → repeat the last sentence only
    - Ideal scenarios for car mode: taxi directions, phone calls, radio announcements
  `,

  // Interface réduite au minimum
  ui: {
    screenAlwaysOn: true,
    displayMode: 'minimal',   // juste le texte du dernier échange
    micAutoRestart: true,     // relance le micro automatiquement après chaque réponse
    fontSize: 'large'         // lisible d'un coup d'œil
  }
};
```

Interface mode voiture :
```
┌─────────────────────────────┐
│  🚗 Mode voiture             │
│─────────────────────────────│
│                              │
│  Gusti :                     │
│  "How much for the airport?" │  ← grande police
│                              │
│  ● En écoute...              │  ← micro toujours actif
│                              │
└─────────────────────────────┘
```

---

### Option clavier — toujours disponible

Le clavier reste accessible via bouton ⌨️ discret.
Utile pour :
- Questions complexes en français qu'on préfère écrire
- Environnement bruyant
- Vérifier l'orthographe d'un mot
- Nuit (ne pas réveiller quelqu'un)

```javascript
// Basculer entre micro et clavier
let inputMode = 'voice';  // 'voice' | 'keyboard'

function toggleInputMode() {
  inputMode = inputMode === 'voice' ? 'keyboard' : 'voice';
  updateUI();
}
```

---

### Lecture vocale des messages Gusti

Chaque message de Gusti est lu automatiquement à voix haute.
Bouton 🔊 pour réécouter si besoin.
Bouton ⏸️ pour mettre en pause.

```javascript
// Après chaque message IA
onAIMessage(raw) {
  const parsed = parseReply(raw);

  // Afficher dans le chat
  renderMessage('ai', parsed);

  // Lire à voix haute (texte anglais principal uniquement)
  gustiSpeak(parsed.text, currentPhase);

  // Les annotations [FR:] [VOCAB:] [✓] sont affichées mais PAS lues
  // → l'utilisateur les lit visuellement après avoir entendu la réponse
}
```

---

### Suivi vocal dans les stats

```javascript
// Tags spécifiques au mode vocal
[OBS:HESITATION_VOCAL:3s]     // pause de 3s avant de répondre
[OBS:REPRISE_MICRO]           // a demandé à réécouter
[OBS:MODE_CLAVIER]            // a basculé sur le clavier pour ce message
[OBS:INCOMPRIS_VOCAL]         // a dit "répète" ou "encore"

// Dans les rapports Gusti :
"Tu hésites encore 2-3 secondes avant de répondre sur les prix.
 Le temps de réaction s'améliore sur le reste.
 Encore quelques semaines et ce sera réflexe."
```

---

### Compatibilité Safari iOS

```javascript
// Vérification au démarrage
function checkVoiceSupport() {
  const hasRecognition = 'webkitSpeechRecognition' in window || 'SpeechRecognition' in window;
  const hasSynthesis = 'speechSynthesis' in window;

  if (!hasRecognition) {
    showWarning("Reconnaissance vocale non disponible — utilise Safari sur iOS 14+");
    inputMode = 'keyboard';  // fallback clavier
  }
  if (!hasSynthesis) {
    showWarning("Synthèse vocale non disponible — les réponses seront texte uniquement");
  }
}

// Initialiser les voix (asynchrone sur iOS)
speechSynthesis.onvoiceschanged = () => {
  voices = speechSynthesis.getVoices();
};

// Workaround iOS : speechSynthesis se coupe si l'écran se verrouille
// → relancer toutes les 10s si en cours de lecture
setInterval(() => {
  if (speechSynthesis.speaking) {
    speechSynthesis.pause();
    speechSynthesis.resume();
  }
}, 10000);
```

---

### Demande de permission micro (première ouverture)

```
┌─────────────────────────────┐
│  🎤 Autoriser le micro       │
│                              │
│  L'appli a besoin du micro   │
│  pour les sessions vocales.  │
│                              │
│  Safari va te demander       │
│  l'autorisation — appuie     │
│  sur "Autoriser".            │
│                              │
│  [Activer le micro]          │
│  [Utiliser le clavier]       │
└─────────────────────────────┘
```

---

## 🔄 RÈGLE ABSOLUE — CONVERSATION CONTINUE ET FLUIDE

**C'EST LA RÈGLE LA PLUS IMPORTANTE DE TOUTE L'APPLI.**
**AUCUN APPUI SUR QUOI QUE CE SOIT PENDANT UNE SESSION.**

Une fois la session lancée, c'est une conversation téléphonique.
Gusti parle. Il se tait. Le micro s'ouvre seul. Tu parles. Le micro se ferme seul. Gusti répond.
En boucle. Sans interruption. Sans bouton. Sans action de l'utilisateur.

Si ce n'est pas fluide → l'utilisateur arrête. C'est aussi simple que ça.

---

### Le cycle automatique complet

```javascript
// ═══════════════════════════════════════════════
// CYCLE CONVERSATION — ENTIÈREMENT AUTOMATIQUE
// ═══════════════════════════════════════════════

async function conversationCycle() {

  // 1. GUSTI PARLE
  await gustiSpeak(currentReply, currentPhase);
  // → microphone COUPÉ pendant que Gusti parle
  // → reconnaissance vocale DÉSACTIVÉE (évite de capter la voix de Gusti)

  // 2. PAUSE NATURELLE
  await sleep(300); // 300ms — comme dans une vraie conversation

  // 3. MICRO S'OUVRE SEUL
  recognition.start();
  showListeningIndicator(); // indicateur visuel discret — pas de bouton

  // 4. DUMÈ PARLE — détection automatique de fin de phrase
  // → silence > 1.5s après parole = fin de réponse
  // → envoi automatique, pas d'appui

  // 5. TRAITEMENT ET RÉPONSE GUSTI
  // → recognition s'arrête automatiquement
  // → appel API
  // → retour au point 1
}

// ── Détection fin de parole (silence automatique) ──
let silenceTimer = null;

recognition.onresult = (event) => {
  const transcript = event.results[0][0].transcript;

  // Afficher le texte en temps réel (interim)
  showInterimText(transcript);

  if (event.results[0].isFinal) {
    clearTimeout(silenceTimer);
    sendToGusti(transcript);
  }
};

recognition.onspeechend = () => {
  // Silence détecté → on attend 1.5s puis on envoie
  silenceTimer = setTimeout(() => {
    recognition.stop();
  }, 1500);
};

// ── Gusti parle → micro coupé ──
function gustiSpeak(text, phase) {
  recognition.abort(); // couper immédiatement le micro
  speechSynthesis.cancel();

  const utterance = new SpeechSynthesisUtterance(stripTags(text));
  utterance.lang = 'en-US';
  utterance.rate = SPEECH_CONFIG[phase].rate;

  // Fin de parole → relancer le cycle
  utterance.onend = () => conversationCycle();

  speechSynthesis.speak(utterance);
}
```

---

### Ce que l'utilisateur voit et fait

```
SESSION LANCÉE
─────────────────────────────────────
Gusti parle                          ← tu écoutes, rien à faire
  [indicateur visuel : onde sonore]

Gusti se tait
  [indicateur visuel : micro actif, petit point qui pulse]

Tu parles                            ← juste parler, rien d'autre
  [ton texte apparaît en temps réel]

Silence 1.5s
  [envoi automatique]

Gusti répond                         ← tu écoutes
─────────────────────────────────────
RÉPÉTER — SANS JAMAIS TOUCHER L'ÉCRAN
```

---

### Indicateurs visuels discrets (pas de boutons)

```css
/* Gusti parle → onde sonore animée */
.wave-indicator {
  display: flex; gap: 3px; align-items: center; justify-content: center;
  height: 24px;
}
.wave-bar {
  width: 3px; background: var(--gold); border-radius: 2px;
  animation: wave 0.8s ease-in-out infinite;
}
.wave-bar:nth-child(2) { animation-delay: 0.1s; }
.wave-bar:nth-child(3) { animation-delay: 0.2s; }
.wave-bar:nth-child(4) { animation-delay: 0.3s; }
@keyframes wave {
  0%,100% { height: 4px; } 50% { height: 20px; }
}

/* Micro actif → point qui pulse */
.mic-active {
  width: 10px; height: 10px; border-radius: 50%;
  background: var(--red);
  animation: pulse 1s ease-in-out infinite;
  margin: 0 auto;
}

/* Traitement → discret */
.processing-dot {
  width: 6px; height: 6px; border-radius: 50%;
  background: var(--muted);
  animation: blink 0.6s ease-in-out infinite;
}
```

---

### Gestion des cas particuliers — toujours sans appui

```javascript
// ── Silence trop long (Dumè ne sait pas quoi dire) ──
// Après 8s sans parole → Gusti relance naturellement
recognition.onspeechstart = () => clearTimeout(longSilenceTimer);

const longSilenceTimer = setTimeout(() => {
  recognition.stop();
  // Gusti dit quelque chose pour relancer, toujours dans la scène
  const nudge = "Take your time... what would you like to say?";
  gustiSpeak(nudge, currentPhase);
}, 8000);

// ── Bruit de fond / faux départ ──
// Si transcript < 2 mots et pas de ? ou . → ignorer, relancer le micro
recognition.onresult = (event) => {
  const transcript = event.results[0][0].transcript.trim();
  if (transcript.split(' ').length < 2) {
    recognition.start(); // relancer sans envoyer
    return;
  }
  sendToGusti(transcript);
};

// ── "Répète" ou "encore" détecté (vocal) ──
// Gusti répète sa dernière phrase et reprend
function handleRepeatRequest() {
  gustiSpeak(lastGustiMessage, currentPhase);
  // Note dans les stats : [OBS:REPRISE_DEMANDEE]
  // En phase 4 : Gusti dit d'abord "In real life I wouldn't repeat — but okay."
}

// ── Question en français détectée ──
// Gusti sort du personnage, répond en français (voix fr-FR), reprend en anglais
function handleFrenchQuestion(transcript) {
  recognition.abort();
  const frResponse = await callAPI(transcript, 'french_question_mode');
  await gustiSpeakFrench(frResponse); // lang: fr-FR
  await sleep(500);
  await gustiSpeak("Alright. Let's continue.", currentPhase);
  recognition.start();
}

// ── Fin de session ──
// Gusti annonce la fin vocalement, lit le bilan, pas de bouton
// "That's it for today. Here's what we worked on..."
// Puis silence → session terminée, retour à l'écran home automatiquement
```

---

### Lancement de session — le seul appui autorisé

**Un seul et unique appui** dans toute l'expérience : le bouton "Commencer" sur l'écran home.
Après ça, plus rien. Zéro interaction requise jusqu'à la fin de la session.

```
Écran home
  [🎯 Commencer]  ← LE SEUL BOUTON QUI EXISTE
      ↓
Session lancée
      ↓
Gusti prend la main
      ↓
Conversation automatique jusqu'à la fin
      ↓
Retour home automatique
```

---

### Mode voiture — identique, zéro différence

En voiture c'est exactement le même fonctionnement.
Pas de mode spécial à activer. La conversation continue automatique
est déjà le comportement par défaut — parfait pour conduire.

```
Tu montes dans la voiture
Tu ouvres l'appli
Tu appuies sur Commencer        ← le seul appui
Tu poses le téléphone
Tu conduis et tu parles
```

---

### Règle de développement — à faire respecter par Claude Code

```
⛔ INTERDIT dans le code :
   - Tout bouton pendant une session active
   - Tout champ texte visible par défaut pendant une session
   - Toute action requise de l'utilisateur entre deux répliques
   - Tout "appuie pour parler" / "appuie pour envoyer"

✅ AUTORISÉ :
   - Un bouton "Commencer" sur l'écran home
   - Un bouton ⚙️ discret pour la config (accessible mais pas visible)
   - Un geste swipe pour terminer la session si besoin
   - Le clavier comme fallback si micro non disponible
     (mais caché par défaut — l'utilisateur ne le voit pas)
```

---

## 🎯 FLUIDITÉ — CONTRAINTES TECHNIQUES ABSOLUES

**La fluidité de la conversation EST le produit.**
Pas les scénarios. Pas les stats. Pas le design.
Si la conversation n'est pas fluide, l'appli ne sert à rien.

Ces contraintes sont non-négociables. Claude Code doit les résoudre toutes.

---

### Contrainte 1 — Gusti n'interrompt jamais Dumè

```javascript
// Gusti ne peut PAS parler si Dumè est en train de parler
// Détection : recognition.onstart → flag isSpeaking = true
// Gusti attend que isSpeaking === false avant de démarrer

let userIsSpeaking = false;

recognition.onspeechstart = () => { userIsSpeaking = true; };
recognition.onspeechend   = () => { userIsSpeaking = false; };

async function gustiSpeak(text, phase) {
  // Attendre que Dumè ait fini
  while (userIsSpeaking) await sleep(100);
  // Puis parler
  speechSynthesis.speak(utterance);
}
```

---

### Contrainte 2 — Dumè ne peut pas couper Gusti (micro coupé)

```javascript
// Pendant que Gusti parle → micro COUPÉ
// Évite de capter la voix de Gusti comme réponse de Dumè

utterance.onstart = () => {
  recognition.abort();
  isGustiSpeaking = true;
};

utterance.onend = () => {
  isGustiSpeaking = false;
  // Pause naturelle puis micro s'ouvre
  setTimeout(() => recognition.start(), 350);
};
```

---

### Contrainte 3 — Zéro blanc technologique

```javascript
// Délai ressenti entre fin de Dumè et début de réponse Gusti : < 500ms
// Stratégie :
// 1. Dès que Dumè commence à parler → préparer la requête API (sans envoyer)
// 2. Dès que silence détecté → envoyer immédiatement
// 3. Pendant le chargement API → Gusti dit une filler phrase naturelle

const FILLER_PHRASES = [
  "Mmm...",
  "Right...",
  "I see...",
  "Okay...",
  "Ah...",
  "Let me think..."
];

async function sendToGusti(transcript) {
  // Lancer la filler immédiatement (< 100ms)
  const filler = FILLER_PHRASES[Math.floor(Math.random() * FILLER_PHRASES.length)];
  gustiSpeakFiller(filler); // très court, < 1s

  // En parallèle → appel API
  const reply = await callAPI(transcript);

  // Gusti enchaîne naturellement sur la filler
  gustiSpeak(reply, currentPhase);
}
```

---

### Contrainte 4 — Gusti attend naturellement si Dumè hésite

```javascript
// Si silence > 2s MAIS Dumè a commencé à parler
// → Gusti attend sans relancer, comme une vraie personne patiente
// Si silence > 8s sans AUCUNE parole
// → Gusti relance naturellement dans la scène (pas de message d'erreur)

let hasStartedSpeaking = false;

recognition.onspeechstart = () => { hasStartedSpeaking = true; };

const waitTimer = setTimeout(() => {
  if (!hasStartedSpeaking) {
    // Gusti relance naturellement
    gustiSpeak("Take your time...", currentPhase);
  }
  // Si a commencé → continuer à attendre
}, 8000);
```

---

### Contrainte 5 — Hésitations non envoyées

```javascript
// "hmm", "euh", "wait", "uh", "um", "let me think"
// → reconnus comme hésitations, pas envoyés comme réponse
// → micro reste ouvert, Gusti attend

const HESITATION_WORDS = [
  'hmm', 'hm', 'euh', 'uh', 'um', 'err',
  'wait', 'let me think', 'how do you say',
  'comment dit-on', 'je sais pas'
];

function isHesitation(transcript) {
  const t = transcript.toLowerCase().trim();
  return HESITATION_WORDS.some(h => t === h || t.startsWith(h + ' '))
    && transcript.split(' ').length <= 3;
}

recognition.onresult = (event) => {
  const transcript = event.results[0][0].transcript.trim();
  if (event.results[0].isFinal) {
    if (isHesitation(transcript)) {
      // Micro reste ouvert, Gusti dit juste "Take your time"
      gustiSpeakFiller("Take your time.");
      recognition.start();
      return;
    }
    sendToGusti(transcript);
  }
};
```

---

### Contrainte 6 — Dumè peut interrompre Gusti

```javascript
// Si Dumè parle pendant que Gusti parle
// → Gusti s'arrête immédiatement (comme une vraie personne)
// → Dumè continue, Gusti écoute

// Surveiller le micro même pendant que Gusti parle
// (en mode écoute passive seulement — détecter la parole sans transcrire)
const interruptRecognition = new SpeechRecognition();
interruptRecognition.continuous = true;
interruptRecognition.interimResults = true;

utterance.onstart = () => interruptRecognition.start();

interruptRecognition.onspeechstart = () => {
  if (isGustiSpeaking) {
    // Dumè interrompt → Gusti s'arrête
    speechSynthesis.cancel();
    isGustiSpeaking = false;
    interruptRecognition.stop();
    // Basculer sur le micro principal pour capter la réponse de Dumè
    setTimeout(() => recognition.start(), 100);
    // [OBS:INTERRUPTION] dans les stats
  }
};
```

---

### Contrainte 7 — Transition entre scènes invisible

```javascript
// Quand Gusti change de scène (hôtel → taxi → restaurant)
// → pas d'annonce "on passe à la scène suivante"
// → transition naturelle dans le dialogue
// → Gusti sort du personnage 2 secondes, commente en français, reprend
// Tout ça en vocal, fluide, sans aucune action de Dumè

// Exemple vocal :
// [fin scène hôtel]
// Gusti (voix fr-FR, détendue) : "Pas mal. Le réceptionniste t'aurait compris."
// [pause 1s]
// Gusti (voix en-US, nouveau personnage) : "Taxi! Where to, sir?"
// → micro s'ouvre, Dumè répond
```

---

### Contrainte 8 — Reprise après interruption réseau

```javascript
// Si l'API met trop longtemps (> 4s) → Gusti dit quelque chose
// pour meubler le silence naturellement

const apiTimeout = setTimeout(() => {
  gustiSpeakFiller("Interesting... give me a moment.");
}, 4000);

const reply = await callAPI(transcript);
clearTimeout(apiTimeout);
gustiSpeak(reply, currentPhase);
```

---

### Test de fluidité — critères de validation

```
✅ Dumè n'appuie sur rien pendant toute la session
✅ Blanc entre répliques < 1s ressenti (filler + API en parallèle)
✅ Gusti s'arrête si Dumè parle par-dessus
✅ "hmm" et "euh" n'envoient pas de message vide
✅ Silence de 8s → Gusti relance naturellement dans la scène
✅ Transition entre scènes : fluide, vocale, naturelle
✅ Question en français → Gusti répond en français → reprend en anglais
✅ Fin de session → bilan vocal → retour home automatique
✅ Fonctionne en voiture sans regarder l'écran
✅ Réseau lent → filler naturelle → pas de silence mort
```

---

### Philosophie de développement

```
La technologie doit être invisible.
L'utilisateur ne sait pas qu'il y a un micro, une API, une synthèse vocale.
Il sait juste qu'il parle à Gusti.
C'est tout.
```
