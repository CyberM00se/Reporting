# Bert.exe Notes

First we scan the target for the port that is open. We can see that port 8777 is on when the bert.exe service is run

<figure><img src="https://lh4.googleusercontent.com/v28-aCuBWTUmSP99RkMhPwoi-TP7Zxp_lXubGDk4Lg7fu0ZdpZU1Zjv7HGKcjEFGwPZ9hN6iULkMQ7I3-lVoA92mRwrnYRr5z3Fp4lUX3-5FSYJ25dbH4kxcvL95fP2MX5WRqQf8CvpsYZg-kWbowDWH1cxrsbmISXFScw_e2nyRIo5eYMdBWA8ZNkFsug" alt=""><figcaption><p>Scanning of bert service</p></figcaption></figure>

checking the commands within the bert.exe program. instead of trun it is bert

<figure><img src="https://lh4.googleusercontent.com/BVDPSz6UJDonnZwIu-7x9CFzK2HgbO5SOBC0DohzAqSNPLaJc5EmGZ-FIXVOvBaPn9ntXSaVjeugUQyRgXsnM2wwbBIP0aIqop2Czan2WMwoGT7NFUvzLWTzV7f-RQwRPtUDXShzUzJQaMFVbaMGCeDVwbT2MS_ZomNlaRzKxlONAdYYfU2-X__-cx2qtg" alt=""><figcaption><p>Connecting and running help</p></figcaption></figure>

Using the same tactics before, we can override the ESP buffer with As

<figure><img src="https://lh5.googleusercontent.com/nIeoflKODQcsfsk46nZUFNnjKezkkbDyT6_7Ao6USBb7_fvfYSmTb__XZUzf9hFn1Xtb0Z2D6pz4lbcQV3pWV3F5pKZLIvcb4AzUbbS98I3AJOfwBJTpC8Kar4qb24ACw_6MRVFAq9MO-2dbE5gasIvJF5G92kcTZIcEvAOF47LbcyYM9PQqrssC35Sn7g" alt=""><figcaption><p>Overflowing ESP with A</p></figcaption></figure>

We can see the value where this starts below.

<figure><img src="https://lh4.googleusercontent.com/N4IDl6tB9k2obIt8eyt1wu5MaMe8JZ2yuaRx8VuLkfQ_FFsZnp8LeF4qbjDmTC_iBKr-L2itXgvV8iXsPMc4rZHLa_mR0l2yRTQzbopWBuq3yf4GM-0isN6Yv0za3kHjwzHEs7J5FznsDDTYe6MW6uyVrOGhMt1K14Fevzv_7JIwM03s3TalkxVqDXy7zw" alt=""><figcaption><p>fuzzing location</p></figcaption></figure>

To make sure the overflow is in the correct location, we override EIP with 42 or B

<figure><img src="https://lh4.googleusercontent.com/64h0QkAIVXo4z8VEWLWBaDYgU6RjD9Hx-dBRCmMJXHxNeOv1ksIqpiV4SUeOnauoPpg_iWjVNeNi6fGdfgDUZZ0f__vkpu6ZBoefHuus3rqE2L16MoYx95jx_mbTPVQxTna1iEbTWayvXcd1Y3BnAzsaqRwJJ2U-dr0gHJYlcwbf1X1K_mqjHrJwNB5oIQ" alt=""><figcaption><p>overriding EIP with B</p></figcaption></figure>

The screenshot below shows the bad chars and all of them working.&#x20;

<figure><img src="https://lh3.googleusercontent.com/4_TzjimI2ZhjY2hMaCd2VpascZ18Gdk3-mMxBjBYIkDJbFfFlUmsi4nYAKFjJVDKaTeNzApYKy-mIfe0TgihVcMrHcRTJzpNsPDxVuokh1bIQ4M3wjO8X9LZnDXpVoXfBdOHPhW9iDL0Q_jjyun3qztDi3nTn1toaqPwxPHLOZqFqoVdbz6ecmutDFja1w" alt=""><figcaption><p>Bad Chars</p></figcaption></figure>

The screenshot below shows the jmp instruction that will be exploited

<figure><img src="https://lh4.googleusercontent.com/G_fdXeOf4-evXNuzBhA5KtrQ8T99-GdfMFSr4dOgmCGkqq5rvhvth5Q3JClGE-ne4L5KR_NC1vcfH3mCWjtes_4Pt65RanlPoeifaMUWgype_jdznooK1rvxc_e0Xy4JSlbojiKHlUm-i_lofoJx_S52uZA6AgtCj0Di2nCHdMM_ofvKsKNgGukT3gc_wQ" alt=""><figcaption><p>exploiting the jump function from the dll</p></figcaption></figure>

Major script changes. The port that is open with this exe is different. It also uses the BERT function instead of TRUN. the offset is different in this also. This ended up being 1753. Lastly, the location of the eip jump function was also different.

<figure><img src="../.gitbook/assets/image (12).png" alt=""><figcaption></figcaption></figure>

This screenshot shows the exploit working, a reverse shell from the windows box using netcat.

<figure><img src="https://lh4.googleusercontent.com/3-RBe54NFbYjVVfHAgQ4PzSEv3FOcODX2DDFxJshBqf79EalGNWtMDWcOOTZTTganMxFIilyqzLxQx4uryBtx7ffDU-2BguSe_Ca44MMDMdViwV1_Q6N3GGKumuGIhn9DLCWWxJ7RgqH39XCXH6N-SZFUg_G_DmTrCZXniRYmTJcbMdgJpEjhvn2zeyv7w" alt=""><figcaption><p>reverse shell from kali to windows</p></figcaption></figure>
