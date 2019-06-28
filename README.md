<!DOCTYPE html>
<html lang="en">
   <head>
      <meta charset="UTF-8">
      <meta name="viewport" content="width=device-width, initial-scale=1.0"/>
      <meta name="author" content=" ">
      <title> </title>
      <link rel="stylesheet" href="./css/shared.css" />
      <style>
         .recognition-options
         {
            list-style: none;
            padding: 0;
         }

         .recognition-options li
         {
            display: inline;
         }

         fieldset
         {
            border: 0;
            margin: 0.5em 0;
            padding: 0;
         }

         legend
         {
            padding: 0;
         }
      </style>
   </head>
   <body>
      <a href="https://www.sitepoint.com/introducing-web-speech-api/"></a>

      <h1>Web Speech API</h1>

      <!-- <p hidden class="js-api-support">API not supported</p> -->

      <div class="js-api-info">
         <h2>Transcription</h2>
         <textarea aria-label="Transcription" id="transcription" class="log" readonly></textarea>

         <form action="" method="get">
            <label for="language">Language:</label>
            <input type="text" id="language" name="language" value="en-US" />

            <fieldset>
               <legend>Results:</legend>
               <ul class="recognition-options">
                  <li>
                     <label>
                        <input type="radio" name="recognition-type" value="final" checked /> Final only
                     </label>
                  </li>
                  <li>
                     <label>
                        <input type="radio" name="recognition-type" value="interim" /> Interim
                     </label>
                  </li>
               </ul>
            </fieldset>

            <button type="button" id="button-play" class="button">Play demo</button>
            <button type="button" id="button-stop" class="button">Stop demo</button>
         </form>

         
      </div>

     

      <script>
         function logEvent(string) {
            var log = document.getElementById('log');

            log.innerHTML = string + '<br />' + log.innerHTML;
         }

         window.SpeechRecognition = window.SpeechRecognition        ||
                                    window.webkitSpeechRecognition  ||
                                    null;

         if (!SpeechRecognition) {
            document.querySelector('.js-api-support').removeAttribute('hidden');
            document.querySelector('.js-api-info').setAttribute('hidden', '');
            [].forEach.call(document.querySelectorAll('form button'), function(button) {
               button.setAttribute('disabled', '');
            });
         } else {
            var recognizer = new SpeechRecognition();
            var transcription = document.getElementById('transcription');

            // Start recognising
            recognizer.addEventListener('result', function(event) {
               transcription.textContent = '';

               for (var i = event.resultIndex; i < event.results.length; i++) {
                  if (event.results[i].isFinal) {
                     transcription.textContent = event.results[i][0].transcript +
                        ' (Confidence: ' + event.results[i][0].confidence + ')';
                  } else {
                     transcription.textContent += event.results[i][0].transcript;
                  }
               }
            });

            // Listen for errors
            recognizer.addEventListener('error', function(event) {
               logEvent('Recognition error: ' + event.message);
            });

            recognizer.addEventListener('end', function() {
               logEvent('Recognition ended');
            });

            document.getElementById('button-play').addEventListener('click', function() {
               transcription.textContent = '';

               // Set if we need interim results
               var isInterimResults = document.querySelector('input[name="recognition-type"][value="interim"]').checked;

               recognizer.lang = document.getElementById('language').value;
               recognizer.continuous = !isInterimResults;
               recognizer.interimResults = isInterimResults;

               try {
                  recognizer.start();
                  logEvent('Recognition started');
               } catch(ex) {
                  logEvent('Recognition error: ' + ex.message);
               }
            });

            document.getElementById('button-stop').addEventListener('click', function() {
               recognizer.stop();
               logEvent('Recognition stopped');
            });

            document.getElementById('clear-all').addEventListener('click', function() {
               document.getElementById('log').textContent = '';
            });
         }
      </script>
   </body>
</html>
