<script>
	import { fade } from 'svelte/transition';
  import { createForm } from "svelte-forms-lib";
  import * as yup from "yup";

	const URL_FN = "https://wizardly-lamport-de3dff.netlify.com/.netlify/functions/save-message";
	let messageSended = false;
	let sendError = false;

	const { form, 
					errors, 
					state, 
					isValid,
					isSubmitting,
					handleChange, 
					handleSubmit, 
					handleReset 
				} 
				= 	createForm({
							initialValues: {
								from: "",
								subject: "",
								body: "",
							},
							validationSchema: yup.object().shape({
								from: yup
									.string()
									.email()
									.required(),
								subject: yup.string().required(),
								body: yup.string().required()
							}),
							onSubmit: values => sendMessage(values)
						});

	async function sendMessage(payload) {	
		sendError = false;
		console.log("send message: ", payload);
		try {
		let response = await fetch(URL_FN, {
  			method: "POST",
				headers: {
						'Accept': 'application/json, text/plain, */*',
						'Content-Type': 'application/json'
				},
				body: JSON.stringify(payload)
		});
		messageSended = true;
		console.log("message sent : " + response);
		} catch(err) {
			sendError = true;
		}
	}
</script>

<main>
<div class="background">
  <div class="contact-form-container">
    <div class="screen">
      <div class="screen-header">
        <div class="screen-header-left">
          <div class="screen-header-button close"></div>
          <div class="screen-header-button maximize"></div>
          <div class="screen-header-button minimize"></div>
        </div>
        <div class="screen-header-right">
          <div class="screen-header-ellipsis"></div>
          <div class="screen-header-ellipsis"></div>
          <div class="screen-header-ellipsis"></div>
        </div>
      </div>
      <div class="screen-body">
				<div class="screen-body-item left">
						{#if sendError}
							<div class="app-title" transition:fade>
							<span>Oops! Something went wrong</span>
							<span>Please try again later</span>
							</div><br/>
						{/if}
						{#if !messageSended}
            <form on:submit={handleSubmit}>
							<div class="app-form-group">
								<input class="app-form-control" 
									id="from"
									name="from"
                  placeholder="Email" 
									on:change={handleChange}
									bind:value={$form.from}>
									{#if $errors.from}
										<small>{$errors.from}</small>
									{/if}
							</div>
	            <div class="app-form-group">
								<input class="app-form-control" 
								id="subject"
								name="subject"
								placeholder="Subject" 
								on:change={handleChange}
								on:blur={handleChange}
								bind:value={$form.subject}
								/>
								{#if $errors.subject}
									<small>{$errors.subject}</small>
								{/if}
							</div>
							<div class="app-form-group">
								<textarea class="app-form-control"
									id="body"
									name="body"
									placeholder="Message"
									on:change={handleChange}
									on:blur={handleChange}
									bind:value={$form.body}
								></textarea>
								{#if $errors.body}
									<small>{$errors.body}</small>
								{/if}
							</div>

							<div class="app-form-group buttons">
              <button class="app-form-button" on:click={handleReset}>Reset</button>
              <button type="submit" class="app-form-button">
							{#if $isSubmitting}Sending...{:else}Send{/if} 
							</button>
            </div>
						</form>			
						{:else}
							<div class="app-title" transition:fade>
            		<span>Your message was successfully sent!</span>
								<br/>
								<br/>
								<br/> 
								<br/> 
								<br/> 
								<br/>
								<br/>
								<br/> 
           		</div>
						{/if}
        </div>
				
        <div class="screen-body-item right">
					{#if !messageSended}
          <div class="app-title">
						<span>Contact</span>
            <span>me</span>
					</div>
					{/if}
          <div class="app-contact"></div>
        </div>
        
      </div>
    </div>
  </div>
</div>

</main>


<style>
*, *:before, *:after {
  box-sizing: border-box;
  -webkit-font-smoothing: antialiased;
  -moz-osx-font-smoothing: grayscale;
}

body {
  background: linear-gradient(to right, #ea1d6f 0%, #eb466b 100%);
  font-size: 12px;
}

body, button, input, textarea {
  font-weight: 700;
  letter-spacing: 1.4px;
}

.background {
  display: flex;
  min-height: 50vh;
}

.contact-form-container {
  flex: 0 1 700px;
  margin: auto;
  padding: 10px;
}

.screen {
  position: relative;
  background: #3e3e3e;
  border-radius: 15px;
}

.screen:after {
  content: '';
  display: block;
  position: absolute;
  top: 0;
  left: 20px;
  right: 20px;
  bottom: 0;
  border-radius: 15px;
  box-shadow: 0 20px 40px rgba(0, 0, 0, .4);
  z-index: -1;
}

.screen-header {
  display: flex;
  align-items: center;
  padding: 10px 20px;
  background: #4d4d4f;
  border-top-left-radius: 15px;
  border-top-right-radius: 15px;
}

.screen-header-left {
  margin-right: auto;
}

.screen-header-button {
  display: inline-block;
  width: 8px;
  height: 8px;
  margin-right: 3px;
  border-radius: 8px;
  background: white;
}

.screen-header-button.close {
  background: #ed1c6f;
}

.screen-header-button.maximize {
  background: #e8e925;
}

.screen-header-button.minimize {
  background: #74c54f;
}

.screen-header-right {
  display: flex;
}

.screen-header-ellipsis {
  width: 3px;
  height: 3px;
  margin-left: 2px;
  border-radius: 8px;
  background: #999;
}

.screen-body {
  display: flex;
}

.screen-body-item {
  flex: 1;
  padding: 30px;
}

.screen-body-item.left {
  display: flex;
  flex-direction: column;
	min-width: 400;
}


.screen-body-item.right {
  display: flex;
  flex-direction: column;
	max-width: 200px;
}

.app-title {
  display: flex;
  flex-direction: column;
  position: relative;
  color: #ea1d6f;
  font-size: 26px;
}

.app-title:after {
  content: '';
  display: block;
  position: absolute;
  left: 0;
  bottom: -10px;
  width: 25px;
  height: 4px;
  background: #ea1d6f;
}

.app-contact {
  margin-top: auto;
  font-size: 8px;
  color: #888;
}

.app-form-group {
  margin-bottom: 15px;
}

.app-form-group.message {
  margin-top: 40px;
}

.app-form-group.buttons {
  margin-bottom: 0;
  text-align: right;
}

.app-form-control {
  width: 100%;
  padding: 10px 0;
  background: none;
  border: none;
  border-bottom: 1px solid #666;
  color: #ddd;
  font-size: 14px;
  outline: none;
  transition: border-color .2s;
}

.app-form-control::placeholder {
  color: #666;
}

textarea {
    padding: 30px;
    border: 0;
    width: 100%;
    font-size: 1rem;
    background-color: #2d2d2d;
    color: white;
    border-radius: 4px;
		min-height: 150px
}

.app-form-control:focus {
  border-bottom-color: #ddd;
}

.app-form-button {
  background: none;
	display: inline-flex;
  border: none;
  color: #ea1d6f;
  font-size: 14px;
  cursor: pointer;
  outline: none;
}

.app-form-button:hover {
  color: #b9134f;
}

.credits {
  display: flex;
  justify-content: center;
  align-items: center;
  margin-top: 20px;
  color: #ffa4bd;
  font-size: 16px;
  font-weight: normal;
}

.credits-link {
  display: flex;
  align-items: center;
  color: #fff;
  font-weight: bold;
  text-decoration: none;
}

.dribbble {
  width: 20px;
  height: 20px;
  margin: 0 5px;
}

@media screen and (max-width: 520px) {
  .screen-body {
    flex-direction: column;
  }

  .screen-body-item.left {
    margin-bottom: 30px;
  }

  .app-title {
    flex-direction: row;
  }

  .app-title span {
    margin-right: 12px;
  }

  .app-title:after {
    display: none;
  }
}

@media screen and (max-width: 600px) {
  .screen-body {
    padding: 40px;
  }

  .screen-body-item {
    padding: 0;
  }
}

</style>