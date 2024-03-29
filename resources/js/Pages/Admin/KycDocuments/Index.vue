<script>
import Template from '{Template}/Admin/Pages/Admin/KycDocuments/Index.template'
import AppLayout from '@/Layouts/AdminLayout'
import Welcome from '@/Jetstream/Welcome'
import NavButtonLink from "@/Jetstream/NavButtonLink";
import Pagination from '@/Jetstream/Pagination'
import SearchFilter from '@/Jetstream/SearchFilter'
import NavLink from "@/Jetstream/NavLink";
import Badge from "@/Jetstream/Badge";
import JetDialogModal from '@/Jetstream/DialogModal'
import JetConfirmationModal from '@/Jetstream/ConfirmationModal'
import JetSecondaryButton from '@/Jetstream/SecondaryButton'
import JetDangerButton from '@/Jetstream/DangerButton'
import pickBy from "lodash/pickBy";
import throttle from "lodash/throttle";
import mapValues from "lodash/mapValues";

export default Template({
    components: {
        Badge,
        NavLink,
        NavButtonLink,
        AppLayout,
        Welcome,
        Pagination,
        SearchFilter,
        JetDialogModal,
        JetSecondaryButton,
        JetConfirmationModal,
        JetDangerButton,
    },
    props: {
        kycDocuments: Object,
        filters: Object,
    },
    data() {
        return {
            selectedAction: null,
            selectedDocument: null,
            documentBeingReviewed: false,
            images: [],
            sending: false,
            showImage: false,
            showReasonModal: false,
            rejectedReason: null,
            rejectedReasonText: "",
            form: {
                search: this.filters.search,
            },
        }
    },
    methods: {
        showModal(images) {
            this.images = images;
            this.showImage = true;
        },
        closeModal() {
            this.showImage = false;
        },
        approve(document) {
            this.selectedDocument = document;
            this.selectedAction = 'approve';
            this.documentBeingReviewed = true;
        },
        reject(document) {
            this.selectedDocument = document;
            this.selectedAction = 'reject';
            this.documentBeingReviewed = true;
        },
        confirmAction() {

            if(this.$page.props.mode == "readonly") {
                return this.$toast.warning('In Demo Version we enabled READ ONLY mode to protect our demo content.')
            }

            let afterRequest = {
                onStart: () => this.sending = true,
                onFinish: () => this.sending = false,
                onSuccess: () => {
                    this.documentBeingReviewed = false;
                    this.$toast.open('Document was moderated');
                },
                onError: () => {
                    this.$toast.error('There are some form errors');
                }
            };

            let form = {
                'action': this.selectedAction
            };

            if(this.selectedAction == "reject") {

                if(this.rejectedReasonText.trim() == "") {
                    alert("Please fill the rejection reason field");
                    return false;
                }

                form.reason = this.rejectedReasonText;
            }

            this.$inertia.put(this.route('admin.kyc.moderate', this.selectedDocument.id), form, afterRequest);
        },
        showReason(document) {
            this.showReasonModal = true;
            this.rejectedReason = document.rejected_reason;
        },
        closeReasonModal() {
            this.showReasonModal = false;
        },
        reset() {
            this.form = mapValues(this.form, () => null)
        },
        getList() {
            let query = pickBy(this.form)
            this.$inertia.replace(this.route('admin.kyc.documents', Object.keys(query).length ? query : { remember: 'forget' }));
        },
    },
    watch: {
        form: {
            handler: throttle(function() {
                this.getList()
            }, 150),
            deep: true,
        },
    },
})
</script>
