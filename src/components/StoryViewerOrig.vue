<template>
    <h1>Story Viewer</h1>
    <button @click="generateContent">Generate</button>
    <div>
      <p>{{ storyText }}</p>
      <img v-if="imageUrl" :src="imageUrl" alt="Generated Image" />
    </div>
</template>

<script lang="ts">
import axios from 'axios';
// import { Configuration, OpenAIApi } from "openai";

export default{
    name: 'StoryViewer',
    data() {
        return {
            storyText: '',
            imageUrl: ''
        }; 
    },
    methods: {
        generateContent(){
            console.log('Generating content...');
            this.storyText = 'Generating content...';
            axios.post('https://api.openai.com/v1/completions', {
                model: "text-davinci-003",
                prompt: "Once upon a time",
                max_tokens: 200,
            },
            {
                headers: {
                    'Content-Type': 'application/json',
                    'Authorization': 'Bearer ${import.meta.env.VITE_OPENAI_API_KEY}'
                }
            }
            )
            .then((response) => {
                
                const generatedText = response.data.choices[0].text.trim();
                this.storyText = generatedText;

                // if the text is generated, use it to generate an image

                axios.post('https://api.openai.com/v1/images/generations', {
                    text: generatedText,
                    num_images: 1,
                },
                    {
                    headers: {
                        'Content-Type': 'application/json',
                        'Authorization': 'Bearer ${import.meta.env.VITE_OPENAI_API_KEY}'
                    }
                    }
            )
            .then((response) => {
                this.imageUrl = response.data.images[0];
            })
            .catch((error) => {
              console.error('Error: Failed to generate image from DALL·E API.', error);
            });
        })
        .catch((error) => {
          console.error('Error: Failed to generate text from GPT API.', error);
        });
        },
    },
};
</script>

<style scoped>
</style>
