<template>
    <b-modal ref="modal" static title="식사 수정">
        <meal-form @error="handleError" @ok="editMeal" ref="meal-form"></meal-form>
        <error-alerts ref="error-alerts"/>

        <template v-slot:modal-footer="{ cancel }">
            <b-button @click="cancel()">
                취소
            </b-button>
            <b-button @click="handleOk" variant="primary">
                저장
            </b-button>
        </template>
    </b-modal>
</template>

<script>
    import MealForm from "@/components/MealForm";
    import {mapGetters} from "vuex";
    import * as MealService from "@/services/meal";
    import ErrorAlerts from "@/components/ErrorAlerts";

    export default {
        name: "meals-edit-meal-modal",
        components: {ErrorAlerts, MealForm},
        data() {
            return {
                origin: undefined
            }
        },
        computed: {
            ...mapGetters({
                currentUser: 'auth/currentUser'
            })
        },
        methods: {
            show(date, meal) {
                this.origin = meal;
                this.$refs['modal'].show();
                this.$refs['meal-form'].initializeForm(date, meal);
            },
            hide() {
                this.$refs['modal'].hide();
            },
            handleOk(event) {
                event.preventDefault();
                this.$refs['meal-form'].ok();
            },
            editMeal(date, body) {
                if (date !== this.origin.date) {
                    body.date = date;
                }
                MealService.updateMeal(this.currentUser.id, this.origin.date, body)
                    .then(() => {
                        this.$emit('updated');
                        this.hide();
                    })
                    .catch(err => this.handleError(err));
            },
            handleError(error) {
                let alerts = this.$refs['error-alerts'];
                if (alerts) {
                    alerts.add(error);
                }
            }
        }
    }
</script>

<style scoped>

</style>