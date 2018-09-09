---
title: 'LastAdapter vs Epoxy'
category: dev
---

Há alguns meses comecei a perceber que muitas das minhas telas eram basicamente uma `ScrollView` gigante com um monte de layouts copiados um embaixo do outro. Isso começou a afetar a complexidade do xml. E nesse tempo eu comecei a comparar com alguns apps que via sendo feitos para iOS que só devolviam `Cell` e `Section` para uma `UITableView`[1].

Nesse momento fiz um teste com o LastAdapter [2] porque queria algo que simplificasse o uso dos meus RecyclerViews e ainda me desse a possibilidade de usar múltiplos tipos na mesma lista.

Esses dias um colega perguntou se eu já tinha usado o Epoxy [3] pois ia precisar fazer uma tela exatamente nesse estilo. Usei um pouco e tentei copiar uma tela de um app pessoal para LastAdapter e Epoxy e comparar a complexidade de escrever as telas com eles.

## Caso de uso

A tela é uma lista simples com contas de banco. O layout do item é simplesmente o nome da conta e o saldo nessa conta.

A fazer um swipe da direita para esquerda o usuário remove o item da lista. Caso essa conta tenha transações associadas a ela, aparece um diálogo dizendo que o usuário deve escolher outra conta para mover as transações ou apagar todas as transações junto com essa conta. Se o usuário escolher mover, aparece um snackbar dizendo para o usuário clicar em outra conta. Quando o usuário seleciona outra conta, a primeira conta é apagada e a segunda é atualizada com o novo valor de saldo.

Existe também um Floating Action Button que cria uma nova conta. Quando a conta é criada a lista atualiza para mostrar a nova conta.

A interação nessa tela parece simples mas com ela é possível testar bastante coisa das duas bibliotecas.

![](/images/20180907_124640-99bff0f8-98ab-45e9-9d97-af512d6d0909.gif)

## Android Data Binding

O Android Data Binding [4] foi uma das ideias do Google de tentar simplificar a conversa entre a view (xml) e um modelo (Activity ou ViewModel). Desde que comecei a usar o LastAdapter alguns meses atrás eu tenho achado a proposta muito interessante. E como o Epoxy tem suporte, quis usar nele também. Só que o uso no Epoxy não é tão simples quanto no LastAdapter.

O layout é a mesmo para ambos, um `<layout>` com `<data>` e é isso. Só que o Epoxy usa um próprio `BindingModel` e precisa que um arquivo `package-info.java` seja criado para gerar esse `BindingModel`. O LastAdapter usa o *Binding do próprio Android Data Binding, não precisa gerar nenhuma classe extra.

## DiffUtil vs ObservableList

O Epoxy tem o `DiffUtil` [5] integrado. Você inclui a lista e ele atualiza a View só com o que foi modificado. Isso é bastante interessante principalmente para as animações de add/delete. E ter esse suporte *out-of-the-box* é o que muita gente quer.

O LastAdapter tem suporte a uma `ObservableList` [6], que é uma lista específica do Android Data Binding. Toda vez que essa lista muda, ela envia um evento para o adapter que atualiza a lista automaticamente. O meio é diferente, o resultado final é o mesmo.

## Renderizar lista

Sempre que algo muda no app, eu envio um evento de render para minha Activity com a nova lista do que ela deve mostrar.

    fun renderAccounts(newAccountList: List<AccountItem>)
{: .language-java}

O Epoxy tem um método para Kotlin `withModels` [7] mas ele foi feito para ser usado em listas que não se modificam. Se você colocar esse metódo dentro do render a lista toda é modificada e pisca para mostrar os novos itens. Ou seja, tenho que criar um `TypedEpoxyController` para a lista. Com esse controller criado, aí sim posso fazer `controller.setData(newTransactionList)` . Meu problema é ter que criar mais classes para fazer essas coisas.

No LastAdapter, como fazer quando a lista muda completamente? Eu posso usar `.clear()` e depois `.addAll(newList)`, mas isso também faz a lista piscar - já que ela foi limpa e depois itens foram adicionados. O que eu fiz nesse caso foi criar uma classe que implementa `ObservableList`, e para as mudanças do `DiffUtil` ele informa através do `DiffUtil.Result` como modificar a lista. Eu peguei essa classe [daqui](https://github.com/evant/binding-collection-adapter/blob/master/bindingcollectionadapter-recyclerview/src/main/java/me/tatarka/bindingcollectionadapter2/collections/DiffObservableList.java) e adicionei alguns métodos que precisava para minha mutable list, como `remove(index)` e `add(index)`. Depois eu criei um `DiffObservableList.Callback` que simplificasse minha vida e eu não precisasse ficar criando `objects` [8] em toda Activity.

Depois de fazer a lista ter suporte a `DiffUtil`, só preciso usar o método que faz update: `transactions.update(newTransactionList)`. Dessa maneira fica tão fácil atualizar a lista quanto com o Epoxy. Só que nesse caso eu só preciso criar 1 classe para qualquer `ObservableList`. Com o Epoxy eu ainda preciso criar 1 controller para cada RecyclerView.

## OnClick, OnBind, OnSwipe

Epoxy tem um helper que ajuda a criar o `OnSwipe` de maneira bem simples com um builder ([aqui](https://github.com/airbnb/epoxy/wiki/Touch-Support#swiping)). LastAdapter não tem isso, mas não é tão difícil de criar um `ItemTouchHelper` [9] e você ainda pode criar uma classe que seja um builder igual do Epoxy. Dessa maneira você só precisa criar uma vez e reutilizar nas suas telas. Inclusive, esse mesmo `ItemTouchHelper` pode ser usado em ambos. *Não testei se o do Epoxy pode ser utilizado no LastAdapter.*

O `OnClick` e o `OnBind` do Epoxy necessitam de um `DataBindingEpoxyModel` ([até onde vi](https://github.com/airbnb/epoxy/wiki/Data-Binding-Support#custom-data-binding-models)). Mais uma classe tendo que ser criada só para gerar esses métodos... Já o LastAdapter te dá isso nos `Type` [10] de uma maneira bem simples. Sim, também é a criação de um classe, mas é bem mais simples ([exemplo](https://gist.github.com/nitrico/88d0e3c78e8a84951e110b3b7d63033c#file-lastadapterwithlisteners-kt)).

## TypedXEpoxyController

Acho que esse foi meu maior problema com o Epoxy. Ou você começa a usar variáveis dentro do `Controller`, ou pode ser que chegue uma hora que não vai ter uma classe `TypedController` para tantos tipos. Um exemplo é um app que fiz que tem **10** tipos diferentes de views.

Cada tipo desse precisa de mais uma variável no Controller, mais um Model (ou pelo menos adicionar no arquivo de geração de models)... Enquanto isso no LastAdapter eu só preciso criar mais uma linha de `map`.

## Conclusão

Depois de tentar fazer as mesmas telas usando ambas as bibliotecas, cheguei a conclusão que o LastAdapter é mais flexível e necessita de menos código para ser usado. Apesar de precisar de um pouco mais de código no início por conta do `DiffUtil` e do `ItemTouchHelper`, a longo prazo menos código é necessário para criar mais tipos de views.

## Dica para OnClick

Como para essas bibliotecas você cria os objetos que vão ser usados no Android Data Binding, uma coisa que ajudou a diminuir ainda mais o código foi definir uma higher order function [11] e colocar direto no objeto. E aí dentro da sua view com Android Data Binding você pode invocar esse método direto do `android:onClick`.

    data class MyItemRow(val onClick: () -> Unit)
    
    No seu ViewModel:
    ...
    val itemRow = MyItemRow({ vm.someCoolMethod() })
    ...
    
    Na sua view:
    <data>
        <variable
            name="item"
            type="com.packager.MyItemRow" />
    </data>
    ...
        <View
        ...
        android:onClick="@{() -> item.onClick.invoke()}"
        ... />
{: .language-kotlin}

## Fontes

1. UITableView: [https://developer.apple.com/documentation/uikit/uitableview](https://developer.apple.com/documentation/uikit/uitableview)
2. LastAdapter: [https://github.com/nitrico/LastAdapter](https://github.com/nitrico/LastAdapter)
3. Epoxy: [https://github.com/airbnb/epoxy](https://github.com/airbnb/epoxy)
4. DataBinding: [https://developer.android.com/topic/libraries/data-binding/](https://developer.android.com/topic/libraries/data-binding/)
5. DiffUtil: [https://developer.android.com/reference/android/support/v7/util/DiffUtil](https://developer.android.com/reference/android/support/v7/util/DiffUtil)
6. ObservableList: [https://developer.android.com/reference/android/databinding/ObservableList](https://developer.android.com/reference/android/databinding/ObservableList)
7. Epoxy.withModels: [https://github.com/airbnb/epoxy/wiki/Basic-Usage#kotlin](https://github.com/airbnb/epoxy/wiki/Basic-Usage#kotlin)
8. Kotlin Objects: [https://kotlinlang.org/docs/reference/object-declarations.html](https://kotlinlang.org/docs/reference/object-declarations.html)
9. ItemTouchHelper: [https://developer.android.com/reference/android/support/v7/widget/helper/ItemTouchHelper](https://developer.android.com/reference/android/support/v7/widget/helper/ItemTouchHelper)
10. LastAdapter doc: [https://medium.com/@miguelangelmoreno/dont-write-recyclerview-adapters-b1dbc2c683bb](https://medium.com/@miguelangelmoreno/dont-write-recyclerview-adapters-b1dbc2c683bb)
11. Kotlin Higher-Order Functions: [https://kotlinlang.org/docs/reference/lambdas.html](https://kotlinlang.org/docs/reference/lambdas.html)