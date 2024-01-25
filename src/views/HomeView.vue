<template>
  <div class="max-w-3xl mx-auto text-gray-700 antialiased divide-y px-4 py-10">
    <nav class="flex items-center justify-between py-4">
        <div class="flex items-center">
          <a href="/">
            <img src="/logos/laravel.svg" alt="Logo" />
          </a>
          <a href="/" class="ml-4">
            <img src="/logos/laravel-word.svg" alt="Logo" />
          </a>
        </div>
        <div class="flex items-center">
            <a href="/" class="ml-4">
                <img src="https://miro.medium.com/v2/resize:fit:720/format:webp/1*OrjCKmou1jT4It5so5gvOA.jpeg" alt="Logo" width="150px" height="250px" />
            </a>
        </div>
        <div>
          <a href="/" class="ml-4">
            <img src="https://picperf.io/https://laravelnews.s3.amazonaws.com/images/tailwindcss-1633184775.jpg" alt="Logo" width="320px" height="205px"/>
          </a>
        </div>
        
      </nav>
    <header
      class="flex flex-col sm:flex-row sm:items-center justify-between py-4"
    >
    <div class="mb-3">
        <div class="text-black font-bold text-xl mb-2">Precisa encurtar uma URL?</div>
        <p class="text-grey-darker text-base">Cole a URL no campo ao lado e a magia acontecerá.</p>
    </div>
    <form class="w-full" v-on:submit.prevent="submit">
        <div class="flex items-center">
            <input class="appearance-none border rounded w-full py-2 px-3 text-gray-700 leading-tight focus:outline-none focus:shadow-outline-none rounded-r-none ml-5 mt-4" id="url" type="url" placeholder="Cole aqui a URL" ria-label="URL" v-model="url">
            <button class="flex-shrink-0 bg-gray-500 hover:bg-gray-700 text-white uppercase font-bold text-sm py-2 px-3 rounded focus:outline-none focus:shadow-outline-none rounded-l-none ml-2 mt-4" type="submit" >
                Encurtar
            </button>
        </div>

        <div class="bg-red-100 border border-red-400 text-red-700 px-3 py-2 rounded relative mt-4" role="alert" v-if="messages.error.length !== 0">
            
            <span class="block sm:inline">{{ this.messages.error }}</span>
        </div>

        <div class="bg-green-100 border border-green-400 text-green-700 px-3 py-2 rounded relative mt-4 ml-5" role="alert" v-if="messages.success.length !== 0">
            <span class="block sm:inline clipboard-text">{{ this.messages.success }}</span>
        </div>
    </form>
    </header>
    <table class="bg-green-100 border border-green-400 text-green-700 px-3 py-2 rounded relative mt-4 " v-if="messages.success.length !== 0">
        <tr>
            <th>URL Original</th>
            <th>URL Reduzida</th>
        </tr>
        <tbody>
            <tr v-for="(url, index) in urls" :key="index">
            <td >{{ truncateURL(url.longURL, 80) }}</td>

            <td ><a class="ml-12" :href="url.shortURL" target="_blank">{{ url.shortURL }}</a></td>
            </tr>
        </tbody>
    </table>
    <main class="py-4">
    </main>
  </div>
</template>

<script >
import axios from 'axios';
    export default {
        

        data() {
            return {
                url: '',
                messages: {
                    error: '',
                    success: ''
                },
                urls: []
            }
        },

        methods: {
            truncateURL(url, maxLength) {
                return url.length > maxLength ? url.substring(0, maxLength) + '...' : url;
            },
            /**
             * Function to validate the given URL
             * 
             * @param  {String} url
             * @return {Boolean}
             */
            validURL: function (url) {
                var re = /^(ftp|http|https):\/\/(([a-zA-Z0-9$\-_.+!*'(),;:&=]|%[0-9a-fA-F]{2})+@)?(((25[0-5]|2[0-4][0-9]|[0-1][0-9][0-9]|[1-9][0-9]|[0-9])(\.(25[0-5]|2[0-4][0-9]|[0-1][0-9][0-9]|[1-9][0-9]|[0-9])){3})|localhost|([a-zA-Z0-9\-\u00C0-\u017F]+\.)+([a-zA-Z]{2,}))(:[0-9]+)?(\/(([a-zA-Z0-9$\-_.+!*'(),;:@&=]|%[0-9a-fA-F]{2})*(\/([a-zA-Z0-9$\-_.+!*'(),;:@&=]|%[0-9a-fA-F]{2})*)*)?(\?([a-zA-Z0-9$\-_.+!*'(),;:@&=\/?]|%[0-9a-fA-F]{2})*)?(\#([a-zA-Z0-9$\-_.+!*'(),;:@&=\/?]|%[0-9a-fA-F]{2})*)?)?$/;

                return re.test(url)
            },
        
           
            submit: function(stURL, event) {
                // Validando a URL fornecida
                if (! this.validURL(this.url)) {
                    this.messages.success = ''
                    this.messages.error = 'Por favor, informe uma URL válida'

                    return false;
                }

                // 
                this.messages.error = ''
                // Chamando a API
                axios.get(`http://tinyurl.com/api-create.php?url=${encodeURIComponent(this.url)}`)

                    .then((response) => {
                        console.log('response' , response)

                        this.messages.success = "Parabéns, essa é sua Url encurtada: " + response.data

                        if (response) {
                            this.urls.push({ longURL: this.url, shortURL: response.data});
                       
                        }
                        // Clear the url from the form
                        this.url = ''
                    })
                    .catch((response) => {
                        this.messages.error = 'Algo deu errado. Por favor tente novamente!'
                    });
                    
            }
        }
    }
</script>
