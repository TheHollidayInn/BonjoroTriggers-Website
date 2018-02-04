<template lang="pug">
  .trigger-editor
    transition(name="slide-fade")
      .row(v-show='!selectedTrigger')
        .col-12.col-md-6.offset-md-3
          h2.trigger-title
            | Triggers
            button.btn.btn-primary(@click='add()') Add a Trigger
          ul.list-group
            li.list-group-item(v-for='(trigger, index) in triggers', :key='trigger._id')
              span {{trigger.name}} {{index}}
              button.btn.btn-danger(@click="removeTrigger(trigger)") Remove
              button.btn.btn-primary(@click="selectTrigger(trigger)") Edit
    transition(name="slide-fade")
      .row(v-show='selectedTrigger')
        .col-12.col-md-6.offset-md-3
          h2 Current Trigger
          div(v-for='(condition, index) in conditions')
            h5(v-if='index === 0') When
            h5(v-if='index > 0') and
            .row
              .col-12.col-md-4
                model-select(:options="options",
                                v-model="condition.item",
                                placeholder="select item")
              .col-12.col-md-4
                select.form-control(v-model='condition.compare')
                  option =
                  option >
                  option >=
                  option <
                  option <=
                  option !=
              .col-12.col-md-4
                input.form-control(v-model='condition.expectedValue', type='text', v-if='typeof exampleObject[condition.item.text] === "string"')
                input.form-control(v-model='condition.expectedValue', type='date', v-if='exampleObject[condition.item.text] instanceof Date')
                input.form-control(v-model='condition.expectedValue', type='number', v-if='typeof exampleObject[condition.item.text] === "number"')
          .row.actions
            .col-12.text-center
              button.btn.btn-danger(@click="remove()") Remove
              button.btn.btn-primary(@click="addAnother()") Add a Condition
          .row.action
            .col-12.text-center
              h2 Then Email Me
              button.btn.btn-primary(@click="finish('')") Finish Editing
</template>

<script>
import axios from 'axios'
import { ModelSelect } from 'vue-search-select'
const uuidv1 = require('uuid/v1')
const camelCase = require('camelcase')

const keysToLabels = {
  firstName: 'First name',
  lastName: 'Last name',
  name: 'Name',
  conversationRating: 'Conversation Rating',
  email: 'Email',
  phone: 'Phone'
}

export default {
  name: 'HelloWorld',
  components: {
    ModelSelect
  },
  data () {
    return {
      triggers: [],
      selectedTrigger: '',
      conditions: [],
      exampleObject: {
        'First name': 'Mike',
        'Last name': 'Roberts',
        'Name': 'Mike Roberts',
        conversationRating: '',
        'Email': 'mike@bonjoro.com',
        phone: '',
        'User ID': '78964335-4c42-4f6c-aa8b-41a77665042c',
        'First Seen (AEDT)': new Date(),
        'Signed up (AEDT)': '5/12/16 2:09',
        'Last seen (AEDT)': '1/18/18 11:24',
        'Last contacted (AEDT)': '8/18/17 13:45',
        'Last heard from (AEDT)': '',
        'Last opened email (AEDT)': '7/6/17 6:40',
        'Last clicked on link in email (AEDT)': '12/20/17 5:42',
        'Web sessions': 150,
        'Country': 'Australia',
        'Region': 'New South Wales',
        'City': 'Sydney',
        'Timezone': 'Australia/NSW',
        'Browser Language': 'en',
        'Language Override': '',
        'Browser': 'dalvik',
        'Browser Version': '2.1.0',
        'OS': '',
        'Twitter Followers': '',
        'Job Title': '',
        'Segment': 'Active,App installed',
        'Tag': 'bonjoro_watched,bonjoro_sent,bonjoro_reacted,bonjoro_shared,[Issue] Upload Slow,M test',
        'Unsubscribed from Emails': 'FALSE',
        'Marked email as spam': 'FALSE',
        'Has hard bounced': 'FALSE',
        'job_title': 'Tech Wizard',
        'api_key': '',
        'greetCount': 231,
        'appInstalled': 1,
        'updatedProfile': 1,
        'addedIntegration': 1,
        'anonymousId': '1e05a18e-2ef8-4180-8aa0-1f353cbb66de',
        'userId': '78964335-4c42-4f6c-aa8b-41a77665042c',
        'id': 'c349d35c-572d-4bbe-8981-2de74d186266',
        'email': 'mike@bonjoro.com',
        'name': 'Bonjoro',
        'referral_url': 'https://www.bonjoro.com/r/sqkUtPHOS0R',
        'phone_number': '',
        'phone_type': '',
        'signup_source': 'unknown',
        'auth_type': 'password',
        'traffic_referrer': '',
        'traffic_landing': '',
        'referrals_accepted': '',
        'greets_completed': 467,
        'lifetime_revenue': '',
        'exception_count': '',
        'onboarding_completed': 'TRUE',
        'autologin_url': '',
        'last_greet_uploaded_at (AEDT)': '2018-01-18 00:34:49 UTC',
        'added_integration': 'TRUE',
        'app_installed': 'TRUE',
        'autologin_app_url': 'https://bonjoro.app.link/VHd5UOtohB',
        'autologin_site_url': 'https://www.bonjoro.com/autologin/FKMPXdyW-cp/78964335-4c42-4f6c-aa8b-41a77665042c',
        'createdAt': 1462982948,
        'created_profile': 'TRUE',
        'rate_opens': 78,
        'rate_views': 48,
        'referrals_count': 0,
        'stat_opens': 421,
        'stat_reactions': 81,
        'stat_views': 462,
        'added_design': 'TRUE',
        'custom_mailer': 'TRUE',
        'article_id': '',
        'Unwatched_Bonjoro': '',
        'firstName': 'Mike',
        'lastName': 'Roberts',
        'Last seen on iOS (AEDT)': '',
        'iOS sessions': 0,
        'iOS App version': '',
        'iOS Device': '',
        'iOS OS version': '',
        'Enabled Push Messaging': '',
        'Is Mobile Unidentified': '',
        'Company name': 'Bonjoro',
        'Company ID': 'c349d35c-572d-4bbe-8981-2de74d186266',
        'Company last seen (AEDT)': '1/19/18 1:16',
        'Company created at (AEDT)': '5/10/16 4:49',
        'People': 18,
        'Company web sessions': 1,
        'Plan': 'usd-default-monthly',
        'Monthly Spend': 0,
        'Company Segment': 'Active',
        'Company tag': '',
        'Company size': '',
        'Company industry': '',
        'Company website': '',
        'account_credit': 3500,
        'monthly_spend': '',
        'owner': '174d5e13-333d-4b9e-b268-9300acfd9517',
        'paid_account': 'FALSE',
        'plan': 'usd-default-monthly',
        'public_billing_url': 'https://www.bonjoro.com/payments/c349d35c-572d-4bbe-8981-2de74d186266',
        'stripe_id': 'cus_9HVVc2NEYaSgl9',
        'createdAt (AEDT)': '2016-05-09 18:49:25 UTC',
        'monthlySpend': 0,
        'creation_source': 'api'
      }
    }
  },
  async mounted () {
    // const localTriggers = localStorage.getItem('bonjoro-triggers')
    // if (localTriggers) {
    //   this.triggers = JSON.parse(localTriggers)
    // }
    const response = await axios.get('https://bonjoro-triggers-api.herokuapp.com/api/v1/triggers')
    this.triggers = response.data.triggers
  },
  computed: {
    fieldOptions () {
      return Object.keys(this.exampleObject)
    },
    options () {
      const options = this.fieldOptions.map(item => {
        const text = keysToLabels[item] ? keysToLabels[item] : item
        return {
          value: camelCase(item),
          text
        }
      })
      return options
    }
  },
  methods: {
    async add () {
      const triggerID = uuidv1()
      const name = `Trigger ${triggerID}`
      const conditions = [
        {
          id: uuidv1(),
          item: {value: '', text: ''},
          compare: '=',
          expectedValue: ''
        }
      ]
      const newTrigger = {
        id: triggerID,
        name,
        conditions
      }

      this.triggers.push(newTrigger)
      this.conditions = newTrigger.conditions

      const response = await axios.post('https://bonjoro-triggers-api.herokuapp.com/api/v1/triggers', {
        name,
        conditions
      })

      newTrigger._id = response.data.trigger._id

      this.selectedTrigger = newTrigger
    },
    selectTrigger (trigger) {
      this.selectedTrigger = trigger
      this.conditions = trigger.conditions
    },
    addAnother () {
      const condition = {
        id: uuidv1(),
        item: {value: '', text: ''},
        compare: '=',
        expectedValue: ''
      }
      this.conditions.push(condition)
    },
    remove () {
      const conditionRemove = this.conditions[this.conditions.length - 1]
      this.conditions = this.conditions.filter(condition => condition.id !== conditionRemove.id)
    },
    removeTrigger (triggerRemove) {
      if (!confirm('Are you sure you want to remove this trigger?')) return
      this.triggers = this.triggers.filter(trigger => trigger._id !== triggerRemove._id)
      axios.delete(`https://bonjoro-triggers-api.herokuapp.com/api/v1/triggers/${triggerRemove._id}`)
      // localStorage.setItem('bonjoro-triggers', JSON.stringify(this.triggers))
    },
    finish () {
      this.selectedTrigger.conditions = this.conditions

      axios.put(`https://bonjoro-triggers-api.herokuapp.com/api/v1/triggers/${this.selectedTrigger._id}`, {
        conditions: this.selectedTrigger.conditions
      })

      this.conditions = []
      this.selectedTrigger = ''

      // localStorage.setItem('bonjoro-triggers', JSON.stringify(this.triggers))
    }
  }
}
</script>

<!-- Add "scoped" attribute to limit CSS to this component only -->
<style scoped>
  h1, h2 {
    font-weight: normal;
  }
  ul {
    list-style-type: none;
    padding: 0;
  }
  li {
    margin: 0 10px;
  }
  a {
    color: #42b983;
  }

  .trigger-editor {
    margin-top: 2em;
  }

  .list-group-item button {
    margin-left: .5em;
  }

  .trigger-title button {
    margin-left: .5em;
  }

  .action {
    margin-top: 2em;
  }

  .actions {
    margin-top: 1em;
  }

  .actions .btn-danger {
    margin-right: 1em;
  }

  .btn-primary {
    background: #27b3c1;
    box-shadow: inset 0 -3px rgba(0,0,0,.3);
  }

  .btn-danger {
    background: #fc5a00;
    border-color: #c94800;
    box-shadow: inset 0 -3px rgba(0,0,0,.3);
  }

  /* Enter and leave animations can use different */
  /* durations and timing functions.              */
  .slide-fade-enter-active {
    transition: all .3s ease;
  }
  .slide-fade-leave-active {
    transition: all .3s cubic-bezier(1.0, 0.5, 0.8, 1.0);
  }
  .slide-fade-enter, .slide-fade-leave-to
  /* .slide-fade-leave-active below version 2.1.8 */ {
    transform: translateX(10px);
    opacity: 0;
  }
</style>
