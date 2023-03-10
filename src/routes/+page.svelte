<script>
  import { onMount } from 'svelte';
  import { Card, CardBody, CardTitle, CardSubtitle, Button, Navbar, NavbarBrand, Styles } from 'sveltestrap';

  let currentJoke = { type: "PLEASE HOLD", setup: "Your joke will display soon", punchline: "I promise"};

  // Will run when component is mounted
  onMount(async () => {
    currentJoke = await fetch('http://localhost:8080/joke').then((x) => x.json());
  });


  function handleAccept() {
    const requestBody = JSON.stringify(currentJoke);

    // Send the POST request
    fetch("http://localhost:8080/joke/accept", {
      method: "POST",
      headers: {
        "Content-Type": "application/json"
      },
      body: requestBody
    })
      .then(response => {
        console.log("Joke posted successfully!");
        // Update currentJoke after successful post
        return fetch('http://localhost:8080/joke').then((x) => x.json());
      })
      .then(joke => {
        currentJoke = joke;
      })
      .catch(error => {
        console.error("Failed to post joke.");
      });
  }

  function handleReject() {
    const requestBody = JSON.stringify(currentJoke);

    // Send the POST request
    fetch("http://localhost:8080/joke/reject", {
      method: "POST",
      headers: {
        "Content-Type": "application/json"
      },
    }).then(response => {
        console.log("Joke rejected successfully!");
        // Update currentJoke after successful rejection
        return fetch('http://localhost:8080/joke').then((x) => x.json());
      }).then(joke => {
        currentJoke = joke;
      }).catch(error => {
        console.error("Failed to reject joke.");
      });
  }
</script>

<Styles />


<Navbar color="light" light expand="md">
  <NavbarBrand href="/">Joke Moderator</NavbarBrand>
</Navbar>
<div class="container">
  <div class="row justify-content-center mt-5">
    <div class="col-md-6">
      <Card>
        <CardBody>
          <CardTitle bind:textContent={currentJoke.type} contenteditable=true class="text-uppercase text-right mb-0">{currentJoke.type}</CardTitle>
          <CardSubtitle class="mb-3"><h2 bind:textContent={currentJoke.setup} contenteditable=true>{currentJoke.setup}</h2></CardSubtitle>
          <h3 bind:textContent={currentJoke.punchline} contenteditable=true>{currentJoke.punchline}</h3>
        </CardBody>
      </Card>
    </div>
  </div>
  <div class="row justify-content-center mt-3">
    <div class="col-md-6">
      <Button color="success" class="mr-2" on:click={handleAccept}>Accept</Button>
      <Button color="danger" class="mr-2" on:click={handleReject}>Reject</Button>
    </div>
  </div>
</div>
