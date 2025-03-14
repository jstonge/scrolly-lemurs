<script>
    import { paint } from '../routes/gradient.js';

    let canvas;

    let todos = [
        `Learn <u><a href="https://svelte.dev/">Svelte</a></u>`,
        `Use <u><a href="https://layercake.graphics/">LayerCake</a></u> to save time`,
        `Do Scrollytelling using <u><a href=https://github.com/the-pudding/svelte-starter/blob/main/src/components/demo/Demo.Scrolly.svelte">Scrolly</a></u>`,
        `Use LLMs to generate relevant CSS and HTML`,
        "Be awesome"
    ];

    $effect(() => {
		const context = canvas.getContext('2d');

		let frame = requestAnimationFrame(function loop(t) {
			frame = requestAnimationFrame(loop);
			paint(context, t);
		});

		return () => {
			cancelAnimationFrame(frame);
		};
	});
</script>



<div class="hero-banner">
    <canvas bind:this={canvas} width={32} height={32}></canvas>

    <!-- GitHub link with logo -->
    <a
    class="github-link"
    href="https://github.com/jstonge/scrolly-lemurs/"
    target="_blank"
    rel="noopener noreferrer"
    >
      <img
        src="https://github.githubassets.com/images/modules/logos_page/GitHub-Mark.png"
        alt="GitHub Logo"
      />
    </a>

    <div class="hero-overlay"></div>
  
    <div class="hero-content">
      <div class="left">
        <h1>Hello Scrollytelling!</h1>
        <p>This landing page is called a hero.</p>
        <p><small>(the headers font is called <u><a href="https://fonts.google.com/specimen/Rubik+Doodle+Shadow">Rubik Doodle Shadow</a>)</u></small></p>
      </div>
      <div class="right">
        <h2>To Do</h2>
        <ul>
          {#each todos as todo}
            <li>{@html todo}</li>
          {/each}
        </ul>
      </div>
    </div>
  </div>
  
  <style>
  /* Import handwritten fonts for a doodle feel */
    @import url('https://fonts.googleapis.com/css2?family=Rubik+Doodle+Shadow&display=swap');

    /* Base hero banner styling */
    .hero-banner {
        position: relative;
        background: #fefefe;
        min-height: 100vh;
        padding: 2rem;
        display: flex;
        align-items: center;
        justify-content: center;
        background: linear-gradient(0.25turn, #3f87a6, #ebf8e1, #f69d3c);
        background-size: cover;
    }

    /* Place the canvas in the bottom right of the hero */
    .hero-banner canvas {
        position: absolute;
        bottom: 0;
        right: 0;
        width: 60vmin;  /* Adjust as needed */
        height: 60vmin;
        background-color: #666;
        mask: url(./ankler.svg) 50% 50% no-repeat;
        mask-size: 60vmin;
        -webkit-mask: url(./ankler.svg) 50% 50% no-repeat;
        -webkit-mask-size: 60vmin;
    }

     /* GitHub link styling */
    .github-link {
      position: absolute;
      top: 1rem;
      right: 1rem;
      z-index: 3; /* Make sure it appears above other elements */
    }

    .github-link img {
      width: 40px;
      height: 40px; /* Optionally, add some hover effects */
      transition: transform 0.2s ease;
    }
    

    /* Content container with a hand-drawn border style */
    .hero-content {
        position: relative;
        z-index: 1;
        display: flex;
        width: 80%;
        max-width: 1200px;
        background: rgba(255, 255, 255, 0.9);
        border: 3px dashed #5f5f5f;
        padding: 2rem;
        border-radius: 10px;
        box-shadow: 5px 5px 15px rgba(0, 0, 0, 0.3);
    }

    /* Two-column layout for left and right sections */
    .hero-content .left,
    .hero-content .right {
        flex: 1;
        padding: 1rem;
    }

    /* A dashed divider between the two sections */
    .hero-content .left {
      border-right: 2px dashed #7e7e7e;
    }

  /* Hand-drawn header styling */
  .hero-content h1 {
    font-family: 'Rubik Doodle Shadow', cursive;
    font-size: 3rem;
    margin-bottom: 1rem;
    margin-top: 4rem;
  }

  .hero-content p {
    font-family: 'Patrick Hand';
    font-size: 1.5rem;
  }

  .hero-content .right h2 {
    font-family: 'Rubik Doodle Shadow';
    font-size: 2.5rem;
    margin-bottom: 1rem;
  }

  /* Todo list styling */
  .hero-content .right ul {
    list-style: none;
    padding: 0;
    margin: 0;
  }

  .hero-content .right li {
    font-family: 'Patrick Hand';
    font-size: 1.5rem;
    margin-bottom: 0.75rem;
    position: relative;
    padding-left: 1.5rem;
  }

  /* A hand-drawn marker icon for each todo */
  .hero-content .right li::before {
    content: '✎';
    position: absolute;
    left: 0;
    top: 0;
    font-size: 1.2rem;
    color: #ff4081;
  }

   /* Responsive adjustments for smaller screens */
   @media (max-width: 768px) {
    .hero-content {
      flex-direction: column;
      border: 2px dashed #ff4081;
    }
    .hero-content .left {
      border-right: none;
      border-bottom: 2px dashed #ff4081;
      margin-bottom: 1rem;
    }
  }


</style>
  