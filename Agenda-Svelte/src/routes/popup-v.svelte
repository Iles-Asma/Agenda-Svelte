<script>
    import { writable } from "svelte/store";
    let getData = localStorage;
    console.log(getData)
</script>

    <div class="popup-v-container">
        <div class="popup-content">
            <h2>Acheter du pain</h2>
            <div class="timeContainer">
                <span>Heure de debut</span>
                <span> - </span>
                <span>Heure de fin</span>
            </div>
            <p class="desc-item">acheter du pain a la boulangerie du coin egzlofbla fenfaenf   kjbezlif blfn ar f azfa</p>
        </div>
    </div>
<style>
    *{
        margin:0;
    }
    .popup-v-container{
        font-family: 'Open Sans', sans-serif;

        border-radius: .5rem;
        padding: 1rem;
        display: flex;
        justify-content: center;
        width: fit-content;
        height: auto;
        background-color: rgb(255, 255, 255);
        position: absolute;

        -webkit-box-shadow: 0px 10px 15px 3px rgba(0,0,0,0.4); 
        box-shadow: 0px 10px 18px 0px rgba(0,0,0,0.4);
    }
    .popup-content{
        width: 500px;
    }

    .desc-item{
        background-color: rgb(225, 225, 225);
        padding: 1rem;
        border-radius: .5rem;
    }
    .timeContainer{
        margin-bottom: 1rem;
        padding-bottom: 1rem;
        border-bottom: 1px rgb(218, 218, 218) solid;
    }

</style>