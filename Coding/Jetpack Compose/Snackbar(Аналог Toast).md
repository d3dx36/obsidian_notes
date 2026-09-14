```
@Composable
fun SnackbarExample() {
    // 1. Создаем состояние для управления Snackbar
    val snackbarHostState = remember { SnackbarHostState() }
    
    // 2. Создаем Scope для запуска корутины (так как показ Snackbar — асинхронный процесс)
    val scope = rememberCoroutineScope()

    Scaffold(
        // Привязываем SnackbarHost к экрану
        snackbarHost = { SnackbarHost(hostState = snackbarHostState) }
    ) { innerPadding ->
        Column(
            modifier = Modifier
                .padding(innerPadding)
                .fillMaxSize(),
            verticalArrangement = Arrangement.Center,
            horizontalAlignment = Alignment.CenterHorizontally
        ) {
            Button(onClick = {
                // 3. Показываем Snackbar внутри корутины
                scope.launch {
                    snackbarHostState.showSnackbar("Действие выполнено успешно!")
                }
            }) {
                Text("Показать Snackbar")
            }
        }
    }
}
```