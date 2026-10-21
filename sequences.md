# ÉTAPE 1

# QUESTION 1: Quel fichier reçoit le clic de l'utilisateur ?
    - MVC: TodoView.js
    - MVP: index.html
    - MVVM: index.html

# QUESTION 2: Quel fichier contient la règle de validation ?
    - MVC: TodoModel.js
    - MVP: LoginPresenter.js
    - MVVM: CartViewModel.js

# QUESTION 3: Qui met l'écran à jour, et comment ?
    - MVC: C'est la Vue. Elle observe le Modèle (le Controller branche la Vue sur le Modèle) pour se mettre à jour
    - MVP: C'est le Presenter. Il le fait par des appels explicites (il commande la Vue)
    - MVVM: C'est la Vue, elle se met à jour toute seule grâce au data binding

# QUESTION 4: Combien de tests ? Ont-ils besoin du navigateur ?
    - MVC: Il possède 3 tests
    - MVP: Il possède 3 tests
    - MVVM: Il possède 2 tests

    - Non ces tests n'ont pas besoin d'un navigateur


# ÉTAPE 2: 

- Diagramme MVC

```mermaid
sequenceDiagram
    actor U as Utilisateur
    participant V as TodoView
    participant C as TodoController
    participant M as TodoModel

    U->>V: saisit un titre et clique « Ajouter »
    V->>V: événement submit, e.preventDefault()
    V->>C: handler(titre) (branché par bindAdd)
    C->>M: add(titre)
    M->>M: trim() puis vérifie que le titre n'est pas vide
    alt titre valide
        M->>M: push({ id, title, done: false })
        M->>M: notify() (méthode privée)
        M-->>C: abonné(todos) (fonction passée à subscribe)
        C->>V: render(todos)
        V->>V: redessine la liste et le compteur
        C->>V: showError("")
    else titre vide
        M-->>C: throw Error("Le titre est obligatoire")
        C->>V: showError(e.message)
    end
    V->>V: vide le champ (input.value = "")
```


- Diagramme MVVM

```mermaid
sequenceDiagram
    actor U as Utilisateur
    participant V as Vue (index.html)
    participant VM as CartViewModel
    participant O as observable (items, count, total)

    Note over V,O: Au chargement, la vue s'abonne (data binding)
    V->>O: items.subscribe(...), count.subscribe(...), total.subscribe(...)

    U->>V: clique « + Livre (25 €) »
    V->>VM: addItem(p) (b.onclick)
    VM->>O: items.get()
    O-->>VM: liste actuelle
    VM->>VM: update([...liste, p]) (méthode privée)
    VM->>O: items.set(nouvelleListe)
    O-->>V: notifie les abonnés de items
    V->>V: redessine la liste ul
    VM->>O: count.set(2)
    O-->>V: notifie les abonnés de count
    V->>V: count.textContent = 2
    VM->>O: total.set(44)
    O-->>V: notifie les abonnés de total
    V->>V: total.textContent = "44 €"
```


# ÉTAPE 3

    - Le message s'affiche sans toucher à la View ni au Controller parce que la règle lève une exception dans TodoModel.add(), que le Controller attrape déjà dans son try/catch pour transmettre e.message à view.showError(), quel que soit le message.