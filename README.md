# SIAEquipe8Hackaton1

# Code Commit

ébauches de code. 

```jsx
type offre = {
  ref_offer_back: nat;
  offer_name: string;
  category_offer: string;
  specification: string;
  state_command: string;
	budgetMin: nat;
  budgetMax: nat;
  last_date: timestamp;
}

type response = {
  id_offer_sc: nat;
	id_response_back: nat;
  form: bytes;
  timestamp: timestamp;
  state_response: string;
}

type interval = {
  id_offer_sc: nat;
	id_response_back: nat;
  form: bytes;
  timestamp: timestamp;
  state_response: string;
}

type storage = {
  offers: big_map(nat, offer);
  responses: big_map(nat, response);
  offerOwners: big_map(nat, address);
  admin: address;
	whitelist: list(adress);
}
```

```jsx
import { TezosToolkit } from '@taquito/taquito';
import { InMemorySigner } from '@taquito/signer';
import contractJSON from './path/to/contract.json';

// Create a new instance of TezosToolkit and set the provider
const Tezos = new TezosToolkit('https://rpc.tezrpc.me');

// Set the signer using an InMemorySigner
const signer = new InMemorySigner('YOUR_PRIVATE_KEY');
Tezos.setProvider({ signer });

// Get the contract instance using the contract address and contract JSON
const contract = await Tezos.contract.at('CONTRACT_ADDRESS', contractJSON);

// Call a contract method
const result = await contract.methods.METHOD_NAME(...ARGS).send();
console.log(result);
```

In this example, we first create a new instance of TezosToolkit and set the provider to a Tezos node. We then create a new signer using an InMemorySigner and set it as the signer for the provider.

Next, we get the contract instance using the contract address and contract JSON file. The contract JSON file contains the contract's interface, which defines the available methods and parameters for the contract.

Finally, we can call a method on the contract using **`contract.methods.METHOD_NAME(...ARGS).send()`**, where **`METHOD_NAME`** is the name of the method we want to call and **`ARGS`** are the arguments we want to pass to the method. The **`send()`** method sends the transaction to the Tezos network and returns a result that we can log to the console or use for further processing.

```jsx
// Get the transaction hash
console.log(`Transaction hash: ${op.hash}`);

// Wait for the transaction to be confirmed
await op.confirmation(1);

// Call a contract method
const op = await contract.methods.entrypoint_name(ARG1, ARG2, ...).send();

// Get the contract instance using the contract address and contract JSON
const contract = await [Tezos.wallet.at](http://tezos.wallet.at/)('CONTRACT_ADDRESS', (await Tezos.contract.abi.get()).find(x => [x.name](http://x.name/) === 'entrypoint_name'));

// Set the signer using an InMemorySigner
const signer = InMemorySigner.fromSecretKey('YOUR_PRIVATE_KEY');
Tezos.setProvider({ signer });

// Create a new instance of TezosToolkit and set the provider
const Tezos = new TezosToolkit('https://YOUR_TEZOS_NODE_ADDRESS.com/');

import { TezosToolkit } from '@taquito/taquito';
import { InMemorySigner } from '@taquito/signer';
import contractJSON from './path/to/contract.json';
```

Note that you need to replace **`YOUR_TEZOS_NODE_ADDRESS`**, **`YOUR_PRIVATE_KEY`**, **`CONTRACT_ADDRESS`**, **`path/to/contract.json`**, **`entrypoint_name`**, **`ARG1`**, **`ARG2`**, etc. with the appropriate values for your specific smart contract and setup.

Also, keep in mind that the code above assumes that you have already compiled your JS Ligo code to Michelson and deployed it on the Tezos network using a tool like **`flextesa`** or **`tezos-client`**.

```jsx
function Selection_of_tender_responses (offer_key: nat, response_keys: list(nat)) : unit is
  block {  
    // Récupération du storage 
    const store : storage = Tezos.get_storage<storage>();  
    
    // Vérification de l'adresse du wallet appelant
    const sender : address = Tezos.get_sender(); 
    if (!Big_map.mem(sender, store.offerOwners[offer_key]) || sender != store.offerOwners[offer_key][sender]) { 
      failwith("Only the offer owner can call this function"); 
    }
    
    // Modification de state_command dans l'offre
    const offer : offer = store.offers[offer_key];
    offer.state_command = "proper_submission";
    store.offers[offer_key] = offer;

    // Modification de state_response dans les réponses sélectionnées
    const valid_responses : list(response) = response_keys.map((key: nat) =>
      block {
        const response : response = store.responses[key];
        if (response.id_offer_sc == offer_key && response.state_response == "pending") {
          response.state_response = "pre-selected";
          store.responses[key] = response;
        };
      });
    
    // Modification de state_response dans les autres réponses 
    const other_responses : list(response) = List.filter((key: nat) =>
      !List.mem(key, response_keys),
      Object.keys(store.responses)
    );
    other_responses.map((key: nat) =>
      block {
        const response : response = store.responses[key];
        if (response.id_offer_sc == offer_key && response.state_response == "pending") {
          response.state_response = "refused";
          store.responses[key] = response;
        };
      });
    
    // Mise à jour du storage
    Tezos.set_storage<storage>(store);
    Unit
  };
```

```jsx

```

```jsx
function Choice_of_contractor(offer_key: nat, response_key: nat) : unit is
  // Récupérer le storage actuel
  var storage = get_storage() : storage;

  // Vérifier que l'appelant est l'offreur
  var offer_owner = Map.find(offer_key, storage.offerOwners) : address;
  if (!Tezos.sender == offer_owner) {
    failwith("Seul le wallet associé à l'offre est autorisé à appeler cet entrypoint");
  }

  // Vérifier que l'état de la commande est correct
  var offer = Map.find(offer_key, storage.offers) : offer;
  if (offer.state_command != "proper_submission") {
    failwith("L'état de la commande n'est pas valide pour sélectionner un entrepreneur");
  }

  // Mettre à jour l'état de la commande de l'offre
  offer.state_command := "contractualisation";

  // Mettre à jour l'état des réponses
  for (var [key, response] in Map.to_iter(storage.responses)) {
    if (response.id_offer_sc == offer_key) {
      if (key == response_key) {
        response.state_response := "selected";
      } else {
        response.state_response := "refused";
      }
      Map.add(key, response, storage.responses);
    }
  }

  // Mettre à jour le storage
  Map.add(offer_key, offer, storage.offers);
  set_storage(storage);
end;
```

```jsx
function adding_propal(form: bytes, response_key: nat): unit is
  // Récupérer le storage actuel
  var storage = get_storage() : storage;

  // Vérifier que l'appelant est l'administrateur
  if (!Tezos.sender == storage.admin) {
    failwith("Vous n'êtes pas autorisé à ajouter une proposition");
  }

  // Récupérer la réponse correspondante
  var response = Map.find(response_key, storage.responses) : response;

  // Vérifier que l'état de la commande est correct
  var offer_key = response.id_offer_sc : nat;
  var offer = Map.find(offer_key, storage.offers) : offer;
  if (offer.state_command != "proper_submission") {
    failwith("L'état de la commande n'est pas valide pour ajouter une proposition");
  }

  // Modifier la spécification de la réponse
  response.specification := form;

  // Mettre à jour la réponse dans le dictionnaire
  Map.add(response_key, response, storage.responses);

  // Mettre à jour le storage
  set_storage(storage);
```

```jsx
function create_offer(ref_offer_back, offer_name, category_offer, specification, state_command, budgetMin, budgetMax, last_date) : unit is
  // Récupérer le storage actuel
  var storage = get_storage() : storage;

  // Vérifier que l'appelant fait partie de la whitelist
  var is_whitelisted = List.contains(storage.whitelist, Tezos.sender) : bool;
  if (!is_whitelisted) {
    failwith("Vous n'êtes pas autorisé à créer des offres");
  }

  // Vérifier que la référence de l'offre n'existe pas déjà
  if (Map.mem(ref_offer_back, storage.offers)) {
    failwith("Cette référence d'offre existe déjà");
  }

  // Créer l'offre
  var new_offer = {
    ref_offer_back: ref_offer_back,
    offer_name: offer_name,
    category_offer: category_offer,
    specification: specification,
    state_command: state_command,
    budgetMin: budgetMin,
    budgetMax: budgetMax,
    last_date: last_date
  } : offer;

  // Ajouter l'offre au dictionnaire des offres
  Map.add(ref_offer_back, new_offer, storage.offers);

  // Enregistrer l'adresse de l'appelant en tant que propriétaire de l'offre
  Map.add(ref_offer_back, Tezos.sender, storage.offerOwners);

  // Mettre à jour le storage
  set_storage(storage)
```

```jsx
function create_response(id_offre_sc, id_response_back, form, horodatage, state_response) : unit is
  // Récupérer le storage actuel
  var storage = get_storage() : storage;

  // Vérifier que l'appelant est l'administrateur
  if (!Tezos.sender == storage.admin) {
    failwith("Vous n'êtes pas autorisé à créer une réponse");
  }

  // Vérifier que l'offre existe
  if (!Map.mem(id_offre_sc, storage.offers)) {
    failwith("L'offre spécifiée n'existe pas");
  }

  // Créer la réponse
  var new_response = {
    id_offer_sc: id_offre_sc,
    id_response_back: id_response_back,
    form: form,
    timestamp: horodatage,
    state_response: state_response
  } : response;

  // Ajouter la réponse au dictionnaire des réponses
  Map.add(id_response_back, new_response, storage.responses);

  // Mettre à jour le storage
  set_storage(storage)
```

```jsx
function choosePlayer(address[] whitelist : list(address)) -> address is
block {
var randomNum : nat = Crypto.sha256(Block.timestamp, Block.hash).to_nat % whitelist.size;
whitelist[randomNum]
} with {
assert(whitelist.size > 0, "Whitelist must not be empty");
}
```

```
cssCopy code
function choosePlayer(address[] whitelist : list(address)) -> address is block { varrandomNum : nat = Crypto.sha256(Block.timestamp, Block.hash).to_nat % whitelist.size; whitelist[randomNum] } with { assert(whitelist.size > 0, "Whitelist must not be empty"); }

```

Explanation of the code:

1. **`function choosePlayer(address[] whitelist : list(address)) -> address is`** - This defines a function named **`choosePlayer`** that takes a list of **`address`** type variables as an argument and returns a single **`address`**.
2. **`block { ... } with { ... }`** - This is a block of code that contains both the logic of the function and its preconditions.
3. **`var randomNum : nat = Crypto.sha256(Block.timestamp, Block.hash).to_nat % whitelist.size;`** - This line generates a random number based on the current block timestamp and hash. It then takes the modulo of this number with the size of the **`whitelist`** list to get a random index.
4. **`whitelist[randomNum]`** - This line returns the **`address`** value at the random index generated in the previous step.
5. **`assert(whitelist.size > 0, "Whitelist must not be empty");`** - This line ensures that the **`whitelist`** list is not empty. If it is empty, it will throw an error message.

Note: This code assumes that the **`whitelist`** list contains only valid Tezos addresses. It is important to ensure that the addresses in the **`whitelist`** list are properly validated before using them in this function.
