.. _secrets-info:

#######
Secrets
#######

Sometimes you may need to store sensitive data, such as a password or token, in
a job file. Each organization has an RSA keypair which can be used to securely
encrypt data which can then be decrypted by the server when the job is run.

Each organization on your Cyclid server has a unique RSA keypair. The public
key can be used to encrypt information which can only be decrypted with the
private key. You can view your organizations public key using the
:ref:`organization show <org-show>` command, but the private key is never
visible and never leaves the server it was created on.

Secrets are decrypted by the Cyclid server using your organizations private
key. The decrypted secrets are added to the :ref:`job context <context>` and
you can then use context variables to insert them where they are required in
the job definition.

You can encrypt new secrets with the :ref:`secret encrypt <secret-encrypt>`
command, which will encrypt a given string with your organizations public RSA
key, encode it with Base64 and print it. You can then copy the encrypted
secret into your job definition.

.. _secrets_example:

*******
Example
*******

Define two different secrets ``github_token`` and ``encfs_passwd``

.. highlight:: json
.. code:: json

  "secrets" : {
    "github_token" : "NST5WwAL7b0JFjO94C9q3mv5F3jus69dBr6o9gwGUG177kk7Y/5spP5P+um+VyhrPwJ44WCuwhr7wCR/UiuBZvoq89tsYXg7uEtCJ9uJP18WHHCW9iguTLyXUxDSfWihP2fPHvEka+8K8A2r1Z0FOvZjXSw4+E/COdUdWLQp4GQTHeRvZGV8FS/onoz5V/SYvozHkH6+tw+ZH0k4mkMKVGBl+VPH+RV4PbL9UfhY4/8ZMoaiSLiWg469a49W80qcimnfR3AP+v6vronoHg+d5mqWH+i0LpUeavMzoQnocQmD7axBs+lfVOKbKa2dPwPxLBdaxs2LPhL+ETwDMWCwaw==",
    "encfs_passwd" : "F6By3LcsONzQ2VJ3OqvImXymwnBCbUpv4JxgmjO52UbMFyErSbSmhP+6PzHmb4LFi7zXel3ujmEk9T5VCibjNkHoTCdKPZ2Pi0hT/ECbVqmPFscd3fjRlqkk2oMss/ZfpLl8NdKMCp3KaxB8w7dHfZq0ZmO3kgSNiD3JL+UsWoWy/K7+r9RiBHggZs7rcdwtGmmC55V6R4AJJeVW5HHg8uq+Crjh6HfYSplgaGgFc3Zhskn/OK9SiuhDIpz4jBtt4rZmicXV4OW/yO81e4sUkFTzlPDSj2EkSyqpz3mBB5Zg1iGp2hqFEn2BTA4Kh0/M00AAHFNg7gtqdHbxUXITXA=="
  }


