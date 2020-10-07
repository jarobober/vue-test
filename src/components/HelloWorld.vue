<template>
  <v-container>
    <v-row class="text-center">
      <v-col cols="12">
        <ApolloQuery
                :query="require('@/graphql/categories.gql')"
                :variables="{ first: firstValue }"
          >
            <template v-slot="{ result: {  data } }">
              <v-simple-table>
                <template v-slot:default>
                  <thead>
                    <tr>
                      <th class="text-left">
                        Id
                      </th>
                      <th class="text-left">
                        Name
                      </th>
                      <th class="text-left">
                        Description
                      </th>
                    </tr>
                  </thead>
                  <tbody>
                    <tr
                      v-for="category in data.categories.edges"
                      :key="category.node.id"
                    >
                      <td class="text-left">
                        {{ category.node.id }}
                      </td>
                      <td class="text-left">
                        {{ category.node.name }}
                      </td>
                      <td class="text-left">
                        {{ category.node.description }}
                      </td>
                    </tr>
                  </tbody>
                </template>
              </v-simple-table>
          </template>
        </ApolloQuery>
        <v-btn class="mt-2" @click="showMore">Show more</v-btn>
      </v-col>
      <v-col cols="6" class="mt-5">
        <v-form ref="form">
          <v-text-field
            v-model="email"
            label="E-mail"
            required
          ></v-text-field>
          <v-text-field
            v-model="password"
            label="Password"
            required
          ></v-text-field>
          <div class="d-flex justify-space-between mt-4">
            <v-btn @click="registerUser()">Sign up</v-btn>
            <v-btn @click="createToken()">Log in</v-btn>
          </div>
        </v-form>
      </v-col>
    </v-row>
  </v-container>
</template>

<script>
import gql from "graphql-tag";

export default {
  name: 'HelloWorld',

  data: function()  {
    return {
      email: '',
      password: '',
      firstValue: 5
    }
  },

  methods: {
     showMore() {
      this.firstValue += 5 
     },
    async registerUser() {
        await this.$apollo.mutate({
        mutation: gql`
          mutation($mail: String!, $pass: String!) {
            accountRegister(input: {
              email: $mail
              password: $pass
              redirectUrl: "https://localhost:3000"
            }), {
              accountErrors {
                message
              }
              requiresConfirmation
              user {
                email
                id
              }
            }
          }
        `,
        variables: {
          email: this.email,
          password: this. password
        }
      })
      alert('Account registered')
    },
    async createToken() {
      await this.$apollo.mutate({
        mutation: gql`
          mutation ($mail: String!, $pass: String!) {
            tokenCreate(email: $mail, password: $pass) {
              token
              user {
                id
                email
              }
              errors {
                field
                message
              }
            }
          }
        `,
        variables: {
          email: this.email,
          password: this. password
        }
      })
      alert('Token created')
    }
  }
}
</script>