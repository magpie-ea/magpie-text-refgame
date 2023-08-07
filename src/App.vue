<template>
  <Experiment title="text-refgame">

    <InstructionScreen :title="'Welcome'">
      <p>Thank you for participating in this experiment!</p>
      There will be <strong>two parts</strong>, each with <strong> {{n_trials}} trials</strong><br>
      In each trial, you will read a short description of a task and are asked to click on an answer.
    </InstructionScreen>

    <InstructionScreen :title="'Get ready for part 1'"/>

    <template v-for="(trial, i) in trialData_production">

    <Screen>

      <Slide>
        <p>Your task is to play a conversation game. There are three objects that you and your friend can see. You have to choose a single word to identify one of the three objects for your friend. The three objects are:</p>

        <p> <strong>
          {{ _.split(trial.objects, "\n")[0] }} <br/>
          {{ _.split(trial.objects, "\n")[1] }} <br/>
          {{ _.split(trial.objects, "\n")[2] }}
          </strong>
        </p>

        <p>Your task is to make your friend pick out the following target object:</p>

        <p><strong>the {{ trial.trigger_object }}</strong></p>

        <p>Which of the following words do you choose:</p>

        <!-- <p> {{_.split(trial.utterances, "\n")[0]}}, -->
        <!--     {{_.split(trial.utterances, "\n")[1]}}, -->
        <!--     {{_.split(trial.utterances, "\n")[2]}}, -->
        <!--     {{_.split(trial.utterances, "\n")[3]}}</p> -->

        <!-- <p>Your answer:</p> -->

        <!-- <p>I would choose the word</p> -->

        <ForcedChoiceInput
            :response.sync= "$magpie.measurements.response"
            :options="trial.utterances_list"
            @update:response="$magpie.saveAndNextScreen();"/>
        <Record :data="{
         'condition': 'production',
         'trial': trial.trial,
         'trial_nr': i + 1 }" />
      </Slide>

    </Screen>

    </template>

    <InstructionScreen :title="'Get ready for part 2'"/>

<template v-for="(trial, i) in trialData_interpretation">

    <Screen>

      <Slide>
        <p>Your task is to play a conversation game. There are three objects that you and your friend can see. Your friend wants to communicate about one of these objects. Your friend selects a single word. Your task is to guess which object your friend is trying to refer to.</p>
        <p>The three objects are:</p>

        <p> <strong>
          {{ _.split(trial.objects, "\n")[0] }} <br/>
          {{ _.split(trial.objects, "\n")[1] }} <br/>
          {{ _.split(trial.objects, "\n")[2] }}
          </strong>
        </p>

        <p>
          Your friend can choose from the following list of words:
        </p>

        <p> <strong>{{_.split(trial.utterances, "\n")[0]}}</strong>,
          <strong>{{_.split(trial.utterances, "\n")[1]}}</strong>,
          <strong>{{_.split(trial.utterances, "\n")[2]}}</strong>,
          <strong>{{_.split(trial.utterances, "\n")[3]}}</strong></p>

        <p>
          Your friend chose the word:
        </p>

        <p><strong>{{ trial.trigger_word }}</strong></p>

        <p>
          Which object do you think your friend is trying to refer to?
        </p>

        <ForcedChoiceInput
            :response.sync= "$magpie.measurements.response"
            :options="trial.objects_list"
            @update:response="$magpie.saveAndNextScreen();"/>
        <Record :data="{
           'condition': 'interpretation',
           'trial': trial.trial,
           'trial_nr': i + n_trials + 1}" />
      </Slide>

    </Screen>

    </template>

    <SubmitResultsScreen />
  </Experiment>
</template>

<script>
import _ from 'lodash';
import trialDataOriginal from '../data/results.csv'

var n_trials = 2
var trialData = _.shuffle(trialDataOriginal).slice(0,2*n_trials)

function addConcatenatedProperty(objectsArray, prop1, prop2) {
    objectsArray.forEach(obj => {
    obj.concatenatedProperty = obj[prop1] + obj[prop2];
  });
}



trialData.forEach(t => {
    t.utterances_list = _.split(t.utterances, "\n")
    t.objects_list    = _.split(t.objects, "\n")
})


export default {
  name: 'App',
  data() {
    return {
        trialData_production: trialData.slice(0,n_trials),
        trialData_interpretation: trialData.slice(n_trials,2*n_trials),
        n_trials: n_trials
    };
  },
  computed: {
    // Expose lodash to template code
    _() {
      return _;
    }
  }
};
</script>
