<script>
import Template from '{Template}/Admin/Pages/Admin/BankAccounts/Index.template'
import AppLayout from '@/Layouts/AdminLayout'
import Welcome from '@/Jetstream/Welcome'
import NavButtonLink from "@/Jetstream/NavButtonLink";
import Pagination from '@/Jetstream/Pagination'
import NavLink from "@/Jetstream/NavLink";
import Badge from "@/Jetstream/Badge";

export default Template({
    components: {
        Badge,
        NavLink,
        NavButtonLink,
        AppLayout,
        Welcome,
        Pagination,
    },
    props: {
        bankAccounts: Object,
    },
    data() {
        return {
            sending: false,
        }
    },
})
</script>
