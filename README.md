# AnkiTools

An Anki \*.apkg and \*.anki2 reader/editor to work with in Python. Also included a module on [AnkiConnect](https://github.com/FooSoft/anki-connect).

## Parsing \*.apkg and \*.anki2 in a human readable and easily manageable format.

```python
from AnkiTools.tools.read import readApkg

with readApkg('Chinese.apkg')) as anki:
    anki.midToModel('xxxxxxx')
    anki.didToDeck('xxxxxxx')
    anki.nidToNote('xxxxxxx')
    anki.cidToCard('xxxxxxx')

Also,

with readAnki2('collection.anki2')) as anki:
    ...
```

Result formats
```
model = {
    'mid': mid,
    'name': v['name'],
    'fields': fieldNames,
    'templates': templateNames
}
deck = {
    'did': did,
    'name': v['name']
}
note = {
    'nid': nid,
    'mid': mid,
    'content': content,
    'tags': tags
}
card = {
    'cid': cid,
    'nid': nid,
    'did': did,
    'ord': ord
}
```

I also added searching with regex
```python
    anki.getDecks('^Chinese::Hanzi')
    anki.getNotesByField(model_id, field_number, regex)
```

For searching cards, you will need querying, which take a little long to load, so I created a separate function `loadQuery()`.
```python
    anki.loadQuery() # Takes around 90 seconds to load
    params = {
        'type': type,
        'key': key,
        'i': field_number or something_of_that_sort
    }
    anki.getCardQuery(regex, params)
```

Query format
```
query = {
    'cid': card['cid'],
    'note': {
        'nid': nid,
        'mid': mid,
        'content': content,
        'tags': tags
    },
    'deck': {
        'did': did,
        'name': v['name']
    },
    'ord': card['ord'],
}
```

See also the \*.apkg format documentation from [Anki decks collaboration Wiki](http://decks.wikia.com/wiki/Anki_APKG_format_documentation) and [AnkiDroid](https://github.com/ankidroid/Anki-Android/wiki/Database-Structure)

## Editing a \*.apkg and \*.anki2 file without Anki

It will also generate a new model/deck/note/card, if one doesn't exist. The ID's are Unix timestamp in milliseconds.

Subdecks can be made by putting in `::`; for example, `Chinese::SpoonFedChinese`.

```python
from AnkiTools.tools.edit import editApkg

with edit.editApkg('Chinese.apkg') as anki:
    anki.updateModels([{
                        'mid': model_id,  # May be left out
                        'name': model_name,
                        'fields': list_of_field_names,
                        'templates': list_of_template_names

                    }])

    anki.updateDecks([{
                        'did': deck_id,  # May be left out
                        'name': deck_name
                    }])

    anki.updateNotes([{
                        'nid': note_id,  # May be left out
                        'mid': model_id,  # Must match existing mid's
                        'content': list_of_field_contents,
                        'tags': list_of_tags
                    }])

    anki.updateCards([{
                        'cid': card_id,  # May be left out
                        'nid': note_id,  # Must match existing nid's
                        'did': deck_id,  # Must match existing did's
                        'ord': order_in_list_of_template_names
                    }])
    anki.updateCardQueries([{
                              'cid': card_id,  # May be left out
                              'note': {
                                           'nid': note_id,  # May be left out
                                           'mid': model_id,  # May be left out
                                           'content': list_of_field_contents,
                                           'tags': list_of_tags
                                       }
                              'deck': {
                                          'did': deck_id,  # May be left out
                                          'name': deck_name
                                      }
                              'model': {
                                           'mid': mid,  # May be left out
                                           'name': v['name'],
                                           'fields': fieldNames,
                                           'templates': templateNames
                                       }
                              'ord': order_in_list_of_template_names,
                          }])
```

## Exporting \*.anki2

```python
from AnkiTools.tools.edit import editAnki2

with edit.editAnki2('Chinese.anki2') as anki:
    anki.export()
```

## AnkiConnect module

```python
from AnkiTools.AnkiConnect import POST

POST('deckNames')
```

You can also specify `params=dict()` in POST. Version is set to `5` as per default. For what you can put in, please refer to [AnkiConnect](https://github.com/FooSoft/anki-connect).
