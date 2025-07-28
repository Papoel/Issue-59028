# Fix Symfony Bug #59028: Nested EnvVarProcessor Issue

<style>
.container {
    max-width: 800px;
    margin: 0 auto;
    padding: 20px;
    font-family: system-ui, -apple-system, BlinkMacSystemFont, 'Segoe UI', Roboto, Oxygen, Ubuntu, Cantarell, 'Open Sans', 'Helvetica Neue', sans-serif;
    line-height: 1.6;
    color: #333;
}

h1 {
    border-bottom: 2px solid #4CAF50;
    padding-bottom: 10px;
    color: #2E7D32;
}

h2 {
    color: #1976D2;
    margin-top: 30px;
    border-left: 4px solid #1976D2;
    padding-left: 10px;
}

.feature-list {
    background-color: #E8F5E9;
    border-left: 4px solid #4CAF50;
    padding: 10px 15px;
    border-radius: 3px;
}

.feature-list li {
    margin-bottom: 8px;
}

.error {
    background-color: #f8d7da;
    border: 1px solid #f5c6cb;
    color: #721c24;
    padding: 15px;
    margin: 15px 0;
    border-radius: 4px;
    font-family: monospace;
    position: relative;
    box-shadow: 0 2px 4px rgba(0,0,0,0.1);
}

.error:before {
    content: "ERROR";
    display: block;
    font-weight: bold;
    margin-bottom: 5px;
    color: #d9534f;
}

.error span {
    display: block;
    padding-left: 20px;
    position: relative;
}

.error span:before {
    content: "❌";
    position: absolute;
    left: 0;
}

.success {
    background-color: #dff0d8;
    border: 1px solid #d6e9c6;
    color: #3c763d;
    padding: 15px;
    margin: 15px 0;
    border-radius: 4px;
    font-family: monospace;
}

.success:before {
    content: "SUCCESS";
    display: block;
    font-weight: bold;
    margin-bottom: 5px;
    color: #5cb85c;
}

.code-block {
    background-color: #f7f7f7;
    border: 1px solid #e1e1e8;
    border-radius: 3px;
    padding: 15px;
    margin: 15px 0;
    overflow-x: auto;
    font-family: monospace;
}

.steps {
    counter-reset: step-counter;
    list-style-type: none;
    padding-left: 0;
}

.steps li {
    position: relative;
    margin-bottom: 10px;
    padding-left: 30px;
}

.steps li:before {
    content: counter(step-counter);
    counter-increment: step-counter;
    position: absolute;
    left: 0;
    top: 0;
    background-color: #1976D2;
    color: white;
    font-weight: bold;
    border-radius: 50%;
    width: 22px;
    height: 22px;
    text-align: center;
    line-height: 22px;
}

.image-container {
    text-align: center;
    margin: 20px 0;
}

.image-container img {
    max-width: 100%;
    height: auto;
    border: 1px solid #ddd;
    border-radius: 4px;
    padding: 5px;
    box-shadow: 0 2px 5px rgba(0,0,0,0.1);
}

.image-container figcaption {
    font-style: italic;
    margin-top: 8px;
    color: #666;
}
.note-box {
    background-color: #e7f3fe;
    border-left: 6px solid #2196F3;
    padding: 15px;
    margin: 15px 0;
    border-radius: 4px;
}

.note-box h3 {
    margin-top: 0;
    color: #1976D2;
}
</style>

<div class="container">

## Problème

Ce projet démontre et corrige le bug [#59028](https://github.com/symfony/symfony/issues/59028) dans Symfony, où la commande `lint:container --resolve-env-vars` échoue lors du traitement d'expressions de variables d'environnement complexes, notamment avec des processeurs imbriqués.

## Solution

<div class="feature-list">
La correction implémente un traitement spécial dans <code>EnvVarProcessor.php</code> pour gérer correctement:

- Les valeurs par défaut vides (<code>default::</code>) suivies de chaînes complexes
- Les références au paramètre <code>container.runtime_mode</code> 
- Les cas où le processeur <code>key</code> reçoit des chaînes complexes
</div>

## Résultats

### Commande Lint Container

<div class="image-container">
    <figure>
        <img src="./docs/lint.png" alt="Lint Container Success">
        <figcaption>La commande <code>php bin/console lint:container --resolve-env-vars</code> s'exécute maintenant avec succès</figcaption>
    </figure>
</div>

### Tests Unitaires 

<div class="image-container">
    <figure>
        <img src="./docs/tests.jpg" alt="Tests Unitaires">
        <figcaption>Les tests unitaires valident tous les cas d'utilisation</figcaption>
    </figure>
</div>

## Comment reproduire le problème

<ol class="steps">
    <li>Cloner ce dépôt</li>
    <li>Exécuter <code>composer install</code></li>
    <li>Sans la correction, exécuter <code>php bin/console lint:container --resolve-env-vars</code> génère l'erreur:</li>
</ol>

<div class="error">
    <span>Resolved value of "default:kernel.runtime_mode:" did not result in an array value.</span>
</div>

## Comment vérifier la correction

<ol class="steps">
    <li>Avec la correction appliquée, exécuter <code>php bin/console lint:container --resolve-env-vars</code></li>
    <li>Le résultat devrait être:</li>
</ol>

<div class="success">
    The container was linted successfully: all services are injected with values that are compatible with their type declarations.
</div>

<ol class="steps" start="3">
    <li>Exécuter les tests unitaires: <code>php bin/phpunit tests/Issue59028Test.php</code></li>
</ol>

## Détails techniques

<div class="code-block">
La correction ciblée préserve le comportement normal de Symfony tout en gérant correctement les cas spéciaux qui causaient l'erreur, évitant ainsi les modifications de configuration dans les projets utilisateurs.
</div>

<div class="note-box">
    <h3>Note sur cette contribution</h3>
    <p>Cette correction a été développée en partie grâce à l'assistance de Claude Sonnet (IA). Par respect pour les conventions et les développeurs de la core team Symfony, ce code est publié sur mon propre repository plutôt que d'être directement soumis au projet principal.</p>
    <p>J'espère que cette solution sera utile à la communauté Symfony en attendant une correction officielle.</p>
</div>

</div>