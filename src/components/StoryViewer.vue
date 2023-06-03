<template>
    <h1>Story Viewer</h1>
    <button @click="generateContent">Generate</button>
    <div>
      <p>{{ storyText }}</p>
      <img v-if="imageUrl" :src="imageUrl" alt="Generated Image" />
    </div>
</template>

<script lang="ts">
// import axios from 'axios';
import { Configuration, OpenAIApi } from "openai";

export default{
    name: 'StoryViewer',
    data() {
        return {
            storyText: '',
            imageUrl: ''
        }; 
    },
    methods: {
        async generateContent(){
            const configuration = new Configuration({
            organization: import.meta.env.VITE_OPENAI_ORGANIZATION,
            apiKey: import.meta.env.VITE_OPENAI_API_KEY,
            });
            
            const openai = new OpenAIApi(configuration);

            const response = await openai.createCompletion({
                model: "text-davinci-003",
                prompt: "Once upon a time, there was a dog named Cornelious. Tell us about Cornelious. Max tokens is 200.",
                max_tokens: 200,
                temperature: 0,
                });
            const generatedText = response.data.choices[0].text.trim();
            this.storyText = generatedText;
            // if the text is generated, use it to generate an image

            const imageResponse = await openai.createImage({
                prompt: "Generate a watercolor images to go with" + this.storyText,
                n: 1,
                size: "512x512",
                });
            console.log(imageResponse.data)
            this.imageUrl = imageResponse.data.data[0].url;
    },
    }
}
</script>

<style scoped>
</style>
