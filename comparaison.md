# ÉTAPE 4

# CRITÈRE 1: Couplage entre la vue et la logique
    - MVC: Moyen. La Vue est abonnée au Modèle et doit connaître la forme de ses données (todos), mais c'est le Controller qui fait le lien entre les deux.
    - MVP: Faible. Le Presenter ne connaît la Vue qu'à travers une interface (getCredentials, showError, showSuccess, setLoading) et ne touche jamais au DOM.
    - MVVM: Très faible. Le ViewModel ne connaît pas du tout la Vue : c'est la Vue qui s'abonne à ses observables (data binding).

# CRITÈRE 2: Ce qu'on peut tester sans navigateur
    - MVC: Le Modèle (TodoModel), car il ne dépend pas du DOM. La Vue et le Controller, eux, ont besoin d'un DOM.
    - MVP: Toute la logique de présentation (LoginPresenter), en lui donnant une fausse vue et un faux service d'authentification.
    - MVVM: Le ViewModel (CartViewModel) : on appelle ses commandes et on vérifie ses observables avec get() ou subscribe().

# CRITÈRE 3: Où valider une saisie
    - MVC: Dans le Modèle (TodoModel.add), car c'est une règle métier. Le Controller se contente d'afficher l'erreur.
    - MVP: Dans le Presenter (LoginPresenter.onSubmit), avant d'appeler le service.
    - MVVM: Dans le ViewModel, avant de modifier les observables.

# CRITÈRE 4: Cas d'usage typique
    - MVC: Applications web classiques côté serveur (Spring MVC, Django) ou petites interfaces simples comme notre Todo-list.
    - MVP: Écrans avec beaucoup de logique de présentation à tester, avec une vue passive (applications Android classiques, WinForms).
    - MVVM: Interfaces très réactives avec beaucoup de données affichées (Vue.js, Angular), comme un panier qui se recalcule tout seul.


# QUESTION DATA: Où vivent les données de la Todo-list, qui peut les modifier, et que se passe-t-il au rechargement ?
    - Les données vivent en mémoire, dans le tableau privé #todos de l'objet TodoModel créé par index.html.
    - Seules les méthodes de TodoModel (add, toggle, remove) peuvent les modifier : le # rend le champ privé, donc tout accès depuis l'extérieur de la classe provoque une erreur.
    - Quand on recharge la page, la mémoire RAM est vidée : un nouveau TodoModel est créé avec un tableau vide et toutes les tâches sont perdues.
    - Pour les conserver, il faut changer uniquement le Modèle : sauvegarder #todos (par exemple dans le localStorage) à chaque #notify(), et les recharger dans le constructeur.
    - Le mieux est de passer un objet de stockage au constructeur du Modèle : on garde ainsi des tests sans navigateur (avec un faux stockage), et la Vue et le Controller ne changent pas.