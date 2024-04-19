<script>
  import { onMount, onDestroy } from 'svelte';

import LoremIpsum from './LoremIpsum.svelte'

	import Introduction from './Introduction.svelte'

  let activeStepIndex = 0;
  let observer;
  let scrollyRef;
  let flourishID = "2339753"; // L'ID de la story Flourish

// le texte des boites
	
  let stepsData = [
    { "text": "<mark style='background-color: #ff8c00; color:white; padding: 2px; border-radius: 5px;'><strong>Dead Ball Era (1901-1920)</strong></mark> - Les règles et les stratégies mises en place ne favorisent pas les batteurs. Les stades sont caverneux et il est presque impossible de frapper un homerun dans beaucoup d'entre eux." },
    { "text": "<mark style='background-color: #ffd700; color:white; padding: 2px; border-radius: 5px;'><strong>Live Ball Era (1920-1941)</strong></mark> - Les règles changent et les joueurs adoptent un nouveau style de frappe qui donne plus de puissance. L'attaque reprends le dessus et les homeruns commencent à prendre de l'importance." },
    { "text": "<mark style='background-color: #9400d3; color:white; padding: 2px; border-radius: 5px;'><strong> Integration, Expansion & Free Agency Era (1942-1993)</strong></mark> - Le baseball évolue à vitesse grand V : les afro-américains peuvent jouer, de nouvelles franchises apparaissent. Les homeruns continuent de s'accumuler. " },
		{ "text": "<mark style='background-color: #32cd32; color:white; padding: 2px; border-radius: 5px;'><strong> Steroid Era (1994-2005)</strong></mark> - Les compteurs explosent, Barry Bonds établit un nouveau record en 2001. La plupart des cogneurs de cette époque seront impliqués plus tard dans des scandales de dopage. " },
		{ "text": "<mark style='background-color: #cd5c5c; color:white; padding: 2px; border-radius: 5px;'><strong>Modern Era (2006-&)</strong></mark> - Plus que jamais les équipes misent sur les homeruns pour produire de l'attaque. A la fin des années 2010, la ligue est accusée à de nombreuses reprises de fabriquer des balles qui volent mieux afin de favoriser encore plus l'attaque." },
  ];

	// Le "moteur" du scrollytelling qui utilise l'Intersection Observer API (en gros, le code observe ce qu'il y a à l'écran)
	// ça on ne touche pas sinon tout se casse !

  onMount(() => {
    observer = new IntersectionObserver((entries) => {
      entries.forEach(entry => {
        const { target, isIntersecting } = entry;
        const index = Array.from(scrollyRef.querySelectorAll('.step')).indexOf(target);
        if (isIntersecting) {
          activeStepIndex = index;
        }
      });
    }, { threshold: 0.6 });

    const stepElements = scrollyRef.querySelectorAll('.step');
    stepElements.forEach(el => observer.observe(el));
  });

  onDestroy(() => {
    observer.disconnect();
  });
</script>

<h1>De 1901 à aujourd'hui : l'évolution du homerun, coup le plus iconique du baseball</h1>

<Introduction/>

<!-- si tu affiches la step_0, alors montre la slide_0 -->
<!-- si tu affiches la step_1, alors montre la slide_1 -->
<!-- si tu affiches la step_2, alors montre la slide_2 -->

<section bind:this={scrollyRef} class="section-container">

	  <div class="foreground">
    {#each stepsData as { text }, index}
      <div class="step" data-step={index}>
        <div class="step-content">
          <p>{@html text}</p> <!-- @html important pour que le css du script soit pris en compte-->
        </div>
      </div>
    {/each}
  </div>
	
  <div class="sticky-background">
    <!-- On affiche la slide Flourish en fonction de l'index -->
		<iframe src={`https://flo.uri.sh/story/${flourishID}/embed#slide-${activeStepIndex}`} title="Contenu interactif ou visuel" class="flourish-embed" frameborder="0" scrolling="no" style="width:100%;height:100vh;"></iframe>
  </div>


</section>


<style>

	/* Ici les valeurs pour l'ensemble de la page > 
	peut nécessiter des modifs de couleurs dans Flourish 
	pour s'assurer que le graphe soit tjs bien visible (titre de graphique noir sur
	fond de page noir,ça ne se voit pas bien...*/

	:global(body) {
    background-color: white; 
		color: black;
		font-family: 'Garamond', sans-serif; 
  }
	
	*{
		box-sizing: border-box;
	}
	.section-container {
    margin-top: 1em;
    text-align: center;
    display: flex;
    flex-direction: row; 
		
  }

  .sticky-background {
    position: sticky;
    top: 0;
    height: 100vh;
    flex: 0 1 60%;
    overflow: hidden;
    z-index: 1;
  }

  .foreground {
    display: flex;
    flex-direction: column;
    align-items: center;
    flex: 0 1 40%;
		margin-bottom: 100vh;
  }

  .step {
    min-height: 100vh;
    display: flex;
    justify-content: center;
    align-items: center;
    padding: 20px;
    position: relative;
    z-index: 2;
    width: 100%;
  }

  .step-content {
    background-color: rgba(245, 245, 245, 0.8);
		box-shadow: 1px 1px 10px rgba(0, 0, 0, 0.2);
    color: black;
    border-radius: 10px;
		border: 5px solid lightblue;
    padding: 0.5rem 1rem;
    display: flex;
    flex-direction: column;
    justify-content: center;
    z-index: 10;
    font-size: 1rem;
    text-align: left;
    width: 100%; 
    max-width: 500px; 
    margin: auto;
  }

	/* Pour adapter la vue en mobile: steps centrées par dessus le graphique */

  @media screen and (max-width: 768px) {
    .section-container {
      flex-direction: column-reverse;
    }
    .sticky-background, .foreground {
      width: 100%; 
    }
    .foreground {
      margin-top: -80vh; 
    }
  }
</style>

