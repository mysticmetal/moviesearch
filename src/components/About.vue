<template>
  <div>
    <v-card>
      <v-card-text>
        <div class="text-xs-center">
          <h2 class="white--text ">
            <v-icon left large>question_answer</v-icon> About</h2>
          <p style="font-style: italic;">"If I could only search movie, series, etc. information from one place..."</p>
        </div>

        <div style="margin-top:50px;">
          <h4 class="white--text">What is this place?</h4>
          <p>Movie Search is a project that gives information about a legitimate movie an TV show as easy as possible.</p>
          <h4 class="white--text">Where did you get the data?</h4>
          <p>Movie/Show data is a property of <a href="http://omdbapi.com/">OMDbAPI</a>.</p>
          <h4 class="white--text">Okay, but what makes this place special?</h4>
          <p>by <a href="http://robertsoriano.com">robertsoriano</a>, this page specializes in simplicity, and speed of results.</p>
          <h4 class="white--text">I want to submit feedback/ask a question/report a bug.</h4>
          <p>Sure, visit my website <a href="http://robertsoriano.com">robertsoriano</a> and click contact.</p>
        </div>
      </v-card-text>
    </v-card>
  </div>
</template>


<script>
export default {
    name: 'aboutpage',
    data() {
        return {

        }
    }
}
</script>
