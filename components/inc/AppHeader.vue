<template>
    <div>
        <nav class="navbar bg-body-tertiary">
            <div class="d-flex justify-between p-5">
                <div class="navbar-brand">Airtribe</div>
                <div>
                    <form class="d-flex" role="search">
                        <input class="form-control me-2" type="search" placeholder="Search" aria-label="Search">
                        <button class="btn btn-outline-success" type="submit">Search</button>
                    </form>
                </div>
            </div>
        </nav>
    </div>
</template>

<script>
export default {
    name: 'AppHeader'
}
</script>

<style scoped>
.d-flex {
    display: flex;

}

.navbar {
    background-color: #343a40;
    color: #ffffff;
}

.navbar-brand {
    font-size: 1.5rem;
    color: #ffffff ;
}

.form-control {
    border-radius: 3px;
    border-color: #ffffff;
    color: #ffffff;
}

.form-control:focus {
    border-radius: 2px;
    border-color: #ffffff;
    box-shadow: none;
}

.btn {
  color: #ffffff;
  padding: 5px;
  font-weight: 14px;
}

.btn-outline-success {
  border-color: #ffffff;
  color: #ffffff;
}
</style>
