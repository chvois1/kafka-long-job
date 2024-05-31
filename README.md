# Consommateur Kafka - Tâches longues

Ce référentiel contient des exemples de gestion de tâches de longue durée à l'aide de Spring et Apache Kafka.

Le projet comporte 5 sous-modules, chacun indépendant les uns des autres afin que toutes les informations nécessaires puissent être trouvées en consultant un seul module.

Tous les sous-modules ont les mêmes classes :

- Application : classe principale pour démarrer le module
- Consommateur : le consommateur kafka qui consomme l'événement et démarre le travail de longue durée
- Contrôleur : contient un point final à appeler qui produit un événement (qui est consommé par le consommateur)
- Une collection postman (kafka-long-running-jobs.postman_collection.json) a été ajoutée avec tous les appels de point de terminaison
- LongRunningJob : une simulation d'un travail de longue durée, effectue un thread.sleep de 10 minutes

## Module: microprocesses

Ce module montre comment fonctionnerait la division d'une tâche en plusieurs microprocessus à exécution plus courte.

## Module: not-async

Ce module focntionne de manière synchrone. Il décrit le comportement par défaut lorsqu'un consommateur Kafka quitte le groupe auquel il appartient avant d'avoit terminé sa tâche de travail.

## Module: spring-async

L'approche la plus simple consiste à ajouter une annotation @Async au travail de longue durée afin qu'il soit exécuté sur un thread séparé.

- Avantage : facile à configurer, si les ressources (utilisation du processeur/mémoire) ne sont pas un problème, cette approche fonctionne bien car elle gérera plusieurs événements simultanément.
- Inconvénient : si le travail de longue durée est gourmand en ressources, la concurrence peut devenir un goulot d'étranglement (utilisation excessive du processeur, problèmes de MOO, etc.). .

## Module: pause-container

Utilise KafkaListenerEndpointRegistry pour que le conteneur d'écoute (conteneur Spring qui contient le consommateur) mette en pause et reprenne le consommateur.

- Avantage : plus de contrôle sur la gestion des erreurs
- Inconvénient : nécessite plus d'efforts que l'approche spring-async.

## Module: pause-container-with-acknowledge

Identique l'exemple précédent mais avec une validation automatique désactivée.

- Avantage : Plus de contrôle et la possibilité de ne pas reconnaître les événements lorsqu'une erreur se produit
- Inconvénient : Plus d'effort de programmation que pour les approches précédentes.