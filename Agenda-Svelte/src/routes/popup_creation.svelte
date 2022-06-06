<script>
    /* ATTENTION ! Dans le projet, lors de la mise en commun de nos composants, il
       faudra faire la commande npm install uuid && npm install yup. De plus, les événements
       sont stockés dans le localstorage sous forme de chaine de caractères donc pour les utiliser / traiter,
       il faudra utiliser la fonction JSON.parse de Javascript.
    */


    // Import

    import { v4 as uuidv4 } from 'uuid';
    import * as yup from 'yup';
    
    /* 
        Cette variable est utilisée pour initialiser la bonne date. 
        Pour m'envoyer la bonne date (date de la case sur laquelle l'utilisateur aura cliqué) 
        pour l'événement, il faudra importer ce fichier donc mon composant ainsi :
            import PopupCreation from './popup_creation.svelte';
        dans la partie <script> du composant où on voudra l'utiliser. Ensuite, 
        en attribut du composant PopupCreation, il suffira initialiser la variable 
        "new_task.start_date" avec la bonne date au format 'yyyy-mm-dd' dès l'apparition du popup
        (au clic sur la case jour où on veut rajouter un événement).

        Exemple disponible dans le fichier exemple.svelte
        
        Plus d'explication : https://betterprogramming.pub/6-ways-to-do-component-communications-in-svelte-b3f2a483913c - Partie 1 (Props)
    
    */
    
    export let start_date;

    /* Variable à utiliser lors de l'import du composant pour rendre visible ou non, le popup.
       Cette variable peut valoir 'none' ou 'flex'. C'est aussi un attribut du composant PopupCreation.
       Comme pour la variable "new_task", il faut importer ce composant puis initialiser l'attribut display
       du composant PopupCreation importé à 'none' ou 'flex' selon si on veut que le popup soit visible ou pas.

       Exemple disponible dans le fichier exemple.svelte
    
    */

    export let display;
    let new_task = {};
    let usual_event = false;
    let frequency;
    let display_frequency = 'none';
    let erreur = [];

    // Schéma de contrôle des entrées du formulaire

    let schema = yup.object().shape({
        title: yup.string().required("Le champ 'Titre' doit être rempli"),
        start_date: yup.date()
                        .required("Le champ 'Date de début' doit être remplie")
                        .typeError(),
        end_date:  yup.date() | undefined,
        start_hour: yup.string()
                       .required("Le champ 'Heure de début' doit être remplie"),
        end_hour: yup.string() | undefined,
        color: yup.string().required(),
        description: yup.string() | undefined
    });

    // Gère la fermeture du popup

    export let close;

    /* Gère la fréquence de l'évenement */

    // Fait apparaître ou non la menu déroulant pour choisir la fréquence de l'évenement

    const manage_frequency = () => {
        display_frequency = usual_event ? 'flex' : 'none';
    };

    // Enregistre les évenements à la fréquence choisie

    let save_repeated_events = (task) => {
        let start_date;

        if (usual_event){
            let event = Object.assign({}, task);

            for (var i=1; i < 6; i++){ // Le chiffre 6 a été choisi de manière aléatoire, il peut être changé

                    start_date = new Date(event.start_date);

                    if (frequency == "year")
                        start_date.setFullYear(start_date.getFullYear() + 1);
                    else {
                        var decalage = frequency == "month" ? 1 : 3;
                        start_date.setMonth(start_date.getMonth() + decalage);
                    }
                    
                    event.start_date = start_date.toJSON().split("T")[0];
                    localStorage.setItem(uuidv4(), JSON.stringify(event));
                }
        }
    };

    // Enregistre l'évenement si toutes les données sont valides, retourne une erreur sinon

    let validate = () => {
        new_task.start_date = start_date;
        schema.validate(new_task, {abortEarly: false})
                .then(() => {
                    erreur = [],
                    localStorage.setItem(uuidv4(), JSON.stringify(new_task)),
                    save_repeated_events(new_task)
                })
                .catch((error) => erreur = error.errors ? Object.values(error.errors): []);
    };
    

</script>


<div class="overlay_popup" style:display={display}>
    <div id="popup_creation">
        
        <div id="div_bouton_close">
            <h2>Ajouter un évenement</h2>
            <button type="button" id="button_close" on:click={close}>X</button>
        </div>

        <!-- Traitement des éventuelles erreurs de saisie -->

        <div id="erreur">
            {#each erreur as err}
                <p>{err}</p>
            {/each}
        </div>

        <form>
            <div id="form_info_popup">
                <p class="p_form">
                    <label for="title"><abbr title="(obligatoire)" class="etoile">*</abbr> Titre : </label>
                    <input type="text" bind:value={new_task.title} id="title" />
                </p>

                <p class="p_form">
                    <label for="start_date"><abbr title="(obligatoire)" class="etoile">*</abbr> Date de début : </label> 
                    <input type="date" id="start_date" bind:value={start_date} />
                </p>
                
                <p class="p_form">
                    <label for="end_date"> Date de fin : </label> 
                    <input type="date" id="end_date" bind:value={new_task.end_date} />
                </p>

                <p class="p_form">
                    <label for="start_hour"><abbr title="(obligatoire)" class="etoile">*</abbr> Heure de début : </label>
                    <input type="text" id="start_hour" bind:value={new_task.start_hour} />
                </p>

                <p class="p_form">
                    <label for="end_hour">Heure de fin : </label> 
                    <input type="text" id="end_hour" bind:value={new_task.end_hour} />
                </p>

                <p class="p_form">
                    <label for="color"><abbr title="(obligatoire)" class="etoile">*</abbr> Couleur : </label>
                
                    <select name="color" bind:value={new_task.color} >
                        <option value="red"> Rouge </option>
                        <option value="green">Vert</option>
                        <option value="blue">Bleu</option>
                        <option value="yellow">Jaune</option>
                        <option value="purple">Violet</option>
                    </select>   
                </p>

                <p class="p_form">
                    <label for="description">Description </label> 
                    <input type="text" id="description" bind:value={new_task.description} />
                </p>

                <p id="repeated_event_paragraph">
                    <input type="checkbox" id="event_repeated" bind:checked={usual_event} on:change={manage_frequency} />
                    <label for="event_repeated">L'évenement est-il répété ? </label> 
                </p>

                <p class="p_form" style:display={display_frequency}>
                    <label for="frequency"> Fréquence : </label>
                
                    <select name="frequency" bind:value={frequency}>
                        <option value="month"> Chaque mois </option>
                        <option value="year"> Chaque année </option>
                        <option value="three_months"> Chaque trimestre</option>
                    </select>   
                </p>


            </div>

            <div id="div_button_validate">
                <input type="button" value="Valider" id="validate_button" on:click={validate} />
            </div>
        </form>
    </div>
</div>

<style>
    .overlay_popup{
        position: fixed;
        top: 0;
        bottom: 0;
        left: 0;
        right: 0;
        background-color: rgba(0, 0, 0, 0.2);
        display: flex;
        justify-content: center;
        align-items: center;

    }

    #popup_creation{
        background-color: white;
        width: 30%;
        padding: 30px;
        min-height: 300px;
    }

    #div_bouton_close{
        display: flex;
        justify-content: space-between;
    }

    #form_info_popup{
        height: 100%;
        width: 97%;
        margin-top: 15px;
        display: flex;
        flex-direction: row;
        flex-wrap: wrap;
    }

    #repeated_event_paragraph{
        width: 50%;
        display: flex;
    }

    #event_repeated{
        margin-right: 9px;
    }

    .p_form {
        width: 100%;
        display: flex;
        justify-content: space-between;
    }

    input[type=text], input[type=date] , select {
        width: 57%;
        margin-top: -10px;
        background-color: #eaeaea;
        border: 0px;
        height: 35px;
    }

    #validate_button{
        padding: 12px;
        width: 25%;
        background-color: rgb(1, 221, 221);
        border: 0;
    }

    #validate_button:hover{
        background-color:  rgba(1, 221, 221, 0.6);
    }

    #div_button_validate{
        display: flex;
        width: 100%;
        flex-direction: row-reverse;
        margin-top: 10px;
    }

    #button_close{
        background-color: white;
        height: 100%;
        padding: 8px 10px 8px 10px;
        font-size: large;
        align-self: center;
        border: 0;
        color: gray;
    }

    #button_close:hover {
        color: white;
        background-color: red;
    }

    .etoile {
        color: red;
        text-decoration: none;
    }

    #erreur{
        color: red;
        text-align: center;
        margin-bottom: 15px;
    }

</style>