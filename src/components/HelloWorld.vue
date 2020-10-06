<template>
  <v-container>
    <v-row class="text-center">
      <v-col cols="12">
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
                v-for="category in categories.edges"
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
            <v-btn @click="accountRegister()">Sign up</v-btn>
            <v-btn @click="tokenCreate()">Log in</v-btn>
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
      categoriesNumber: 10
    }
  },

  methods: {
    accountRegister() {
      const mail = this.email
      const pass = this.password
      this.$apollo.mutate({
        mutation: gql`
          mutation {
            accountRegister(input: {
              email: $email
              password: $password
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
          email: mail,
          password: pass
        }
      })
      alert('Account registered')
    },
    tokenCreate() {
      const mail = this.email
      const pass = this.password
      this.$apollo.mutate({
        mutation: gql`
          mutation {
            tokenCreate(email: $email, password: $password) {
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
          email: mail,
          password: pass
        }
      })
      alert('Token created')
    }
  },

  apollo: {
    categories: gql`
      query {
        categories(first: 5) {
          edges {
            node {
              id
              name
              description
            }
          }
        }
      }
    `
  }
}
</script>