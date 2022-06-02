# iWallet scanner Android SDK
## Installation

Add to your build.gradle file
```groovy
implementation('com.github.iwalletinc:android_sdk:$latest_version') { transitive = true } 
```
## Usage
* Create an instance of ChecksApi class, put to constructor your unique key given by iWallet: 
```kotlin
val checksApi = ChecksApi(publishableKey = "unique-key")
```
* Launch IWalletScanCheckActivity to open camera and capture the check:
```kotlin
val getBitmap =
    	registerForActivityResult(ActivityResultContracts.StartActivityForResult()) { result ->
        	if (result.resultCode == Activity.RESULT_OK) {
            	result.data?.let {
                val byteArray: ByteArray? = it.getByteArrayExtra(ApiConstants.IWALLET_FRONT_CHECK)
                if (byteArray !== null) {
                    val image = BitmapFactory.decodeByteArray(byteArray, 0, byteArray.size)
                	}
            	}
        	}

    	}
	getBitmap.launch(Intent(this, IWalletScanCheckActivity::class.java)) 
```
* You can do with image what you need and than pass it to processCheckImage():
```kotlin
fun processCheckImage(
    processCheckCallback: ProcessCheckCallback,
    check: Bitmap,
    amount: Double,
    phone: String,
    note: String? = null,
    stage: Boolean = false
) {
```
* `amount`, `phone` and `check` image is required, also you could pass `note`.

* Also you could set `stage` to true and test how it works with our stage server (fake transactions):
```kotlin
checksApi.processCheckImage(
    processCheckCallback = processCheckCallback,
    amount = binding.etAmount.text.toString().toDoubleOrNull() ?: 0.00,
    phone = binding.etPhone.text.toString(),
    check = image,
    stage = true
)
```
* processCheckCallback is a callback which is triggered when we get a response from the server, you will work with it like this:
```kotlin
private val processCheckCallback = object : ProcessCheckCallback {
    override fun onSuccess(check: CheckResponse) {
        Log.e("check", check.gid)
        Log.e("check", check.message)
    }

    override fun onError(error: ApiError) {
        Log.e("check", error.message)
        when(error) {
            is ApiError.ServerError -> TODO()
            is ApiError.AuthorizedError -> TODO()
            is ApiError.ForbiddenError -> TODO()
            is ApiError.NetworkError -> TODO()
            is ApiError.NotFoundError -> TODO()
            is ApiError.UnprocessableEntityError -> TODO()
        }
    }
}
```
